<properties
    pageTitle="À l’aide de PowerShell Azure avec stockage Azure | Microsoft Azure"
    description="Apprenez à utiliser des applets de commande PowerShell de Azure pour stockage Azure pour créer et gérer des comptes de stockage ; travailler avec des objets BLOB, des tables, des files d’attente et des fichiers ; configurer la requête analytique de stockage et créer des signatures d’accès partagé."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>À l’aide de PowerShell Azure avec stockage Azure

## <a name="overview"></a>Vue d’ensemble

PowerShell Azure est un module qui fournit des applets de commande pour gérer les Azure via Windows PowerShell. Il s’agit d’un environnement de ligne de commande des tâches et un langage de script conçu spécialement pour l’administration du système. Avec PowerShell, vous pouvez facilement contrôler et d’automatiser l’administration de vos applications et de services Azure. Par exemple, vous pouvez utiliser les applets de commande pour effectuer les mêmes tâches que vous pouvez effectuer via le [Portail Azure](https://portal.azure.com).

Dans ce guide, nous allons explorer comment utiliser les [Applets de commande de stockage Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) pour effectuer diverses tâches de développement et d’administration avec le stockage Azure.

Ce guide suppose que vous disposez d’une expérience préalable à l’aide du [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/) et [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Le guide fournit un certain nombre de scripts pour illustrer l’utilisation de PowerShell avec stockage Azure. Vous devez mettre à jour les variables de script en fonction de votre configuration avant d’exécuter chaque script.

La première section de ce guide fournit un coup de œil au stockage Azure et PowerShell. Pour obtenir des informations détaillées et des instructions, démarrez à partir de la [Configuration requise pour l’utilisation de PowerShell Azure avec stockage Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Mise en route avec le stockage Azure et PowerShell dans 5 minutes

Cette section vous indique comment accéder au stockage Azure via PowerShell dans 5 minutes.

**Pour Azure :** Obtenir un abonnement Microsoft Azure et un compte Microsoft associé à cet abonnement. Pour plus d’informations sur les options d’achat d’Azure, consultez [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Offre des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes de Microsoft).

Pour plus d’informations sur les abonnements d’Azure, consultez [affectation les rôles d’administrateur dans Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Après avoir créé un compte et un abonnement Microsoft Azure :**

1.  Téléchargez et installez [PowerShell d’Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Démarrer Windows PowerShell Scripting environnement intégré (ISE) : Sur votre ordinateur local, accédez au menu **Démarrer** . Tapez les **Outils d’administration** , puis cliquez sur Exécuter. Dans la fenêtre **Outils d’administration** , avec le bouton droit de **Windows PowerShell ISE**, cliquez sur **Exécuter en tant qu’administrateur**.
3.  Dans **Windows PowerShell ISE**, cliquez sur **le fichier** > **Nouveau** pour créer un nouveau fichier de script.
4.  Désormais, vous y trouverez un script simple qui affiche les commandes de base PowerShell pour accéder au stockage d’Azure. Le script demande tout d’abord votre Azure compte des informations d’identification de compte à ajouter votre Azure à l’environnement PowerShell locale. Puis, le script va définir l’abonnement Azure par défaut et créer un nouveau compte de stockage dans Azure. Ensuite, le script crée un nouveau conteneur dans ce nouveau compte de stockage et télécharger un fichier image existant (blob) à ce conteneur. Après que le script répertorie tous les objets BLOB dans ce conteneur, il va créer un nouveau répertoire de destination sur votre ordinateur local et télécharger le fichier image.
5.  Dans la section de code suivante, sélectionnez le script entre les notes **#begin** et **#end**. Appuyez sur CTRL + C pour le copier dans le Presse-papiers.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  Dans **Windows PowerShell ISE**, appuyez sur CTRL + V pour copier le script. Cliquez sur **le fichier** > **Enregistrer**. Dans la boîte de dialogue **Enregistrer sous** , tapez le nom du fichier de script, tels que « mystoragescript ». Cliquez sur **Enregistrer**.

6.  Maintenant, vous devez mettre à jour les variables de script en fonction de vos paramètres de configuration. Vous devez mettre à jour la variable **$SubscriptionName** avec votre abonnement. Vous pouvez conserver les autres variables comme spécifié dans le script ou les mettre à jour comme vous le souhaitez.

    - **$SubscriptionName :** Vous devez mettre à jour cette variable avec le nom de votre abonnement. Suivez l’une des trois méthodes suivantes pour trouver le nom de votre abonnement :

        une barre d’outils. Dans **Windows PowerShell ISE**, cliquez sur **le fichier** > **Nouveau** pour créer un nouveau fichier de script. Copiez le script suivant dans le nouveau fichier de script et cliquez sur **Debug** > **exécuter**. Le script suivant demande tout d’abord votre Azure informations d’identification de compte à ajouter votre Azure compte pour l’environnement PowerShell local et afficher tous les abonnements qui sont connectés à la session PowerShell locale. Notez le nom de l’abonnement que vous souhaitez utiliser, en suivant ce didacticiel :

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Pour rechercher et copier le nom de votre abonnement dans le [Portail Azure](https://portal.azure.com), dans le menu de Hub à gauche, cliquez sur **abonnements**. Copier le nom de l’abonnement que vous souhaitez utiliser lors de l’exécution des scripts dans ce guide.

        ![Azure Portal][Image2]

        c. Pour rechercher et copier le nom de votre abonnement dans le [Portail classique d’Azure](https://manage.windowsazure.com/), faites défiler et cliquez sur **paramètres** , sur le côté gauche du portail. Cliquez sur **abonnements** pour afficher la liste de vos abonnements. Copier le nom de l’abonnement que vous souhaitez utiliser lors de l’exécution des scripts donnés dans ce guide.

        ![Azure Portal classique][Image1]

    - **$StorageAccountName :** Utiliser le nom spécifié dans le script, ou entrez un nouveau nom pour votre compte de stockage. **Important :** Le nom du compte de stockage doit être unique dans Azure. Il doit être en minuscules, trop !

    - **$Location :** Utilisez le « US-ouest » donné dans le script ou choisissez d’autres emplacements d’Azure, tels que les États-Unis, l’Europe du Nord et ainsi de suite.

    - **$ContainerName :** Utiliser le nom spécifié dans le script ou entrez un nouveau nom pour votre conteneur.

    - **$ImageToUpload :** Entrez un chemin d’accès à une image sur votre ordinateur local, tel que : « C:\Images\HelloWorld.png ».

    - **$DestinationFolder :** Entrez un chemin d’accès vers un répertoire local pour stocker les fichiers téléchargés depuis le stockage Azure, par exemple : « C:\DownloadImages ».

7.  Après la mise à jour les variables de script dans le fichier « mystoragescript.ps1 », cliquez sur **le fichier** > **Enregistrer**. Ensuite, cliquez sur **Déboguer** > **exécuter** ou appuyez sur **F5** pour exécuter le script.  

Une fois que le script s’exécute, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargé. La capture d’écran suivante montre un exemple de résultat :

![Télécharger des objets BLOB][Image3]


> [AZURE.NOTE] La section « Mise en route avec stockage Azure et PowerShell dans 5 minutes » fourni une brève introduction sur l’utilisation de PowerShell d’Azure avec stockage Azure. Pour obtenir des informations détaillées et des instructions, nous vous invitons à lire les sections suivantes.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Conditions préalables à l’utilisation de PowerShell d’Azure avec stockage Azure
Vous avez besoin d’un abonnement Azure et un compte pour exécuter les applets de commande PowerShell figurant dans ce guide, comme décrit ci-dessus.

PowerShell Azure est un module qui fournit des applets de commande pour gérer les Azure via Windows PowerShell. Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Nous vous recommandons de télécharger et installer ou mettre à niveau vers le dernier module Azure PowerShell avant d’utiliser ce guide.

Vous pouvez exécuter les applets de commande dans la console Windows PowerShell standard ou le Windows PowerShell Integrated Scripting Environment (ISE). Par exemple, pour ouvrir **Windows PowerShell ISE**, allez dans le menu Démarrer, entrez outils d’administration et cliquez sur Exécuter. Dans la fenêtre Outils d’administration, avec le bouton droit de Windows PowerShell ISE, cliquez sur Exécuter en tant qu’administrateur.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Comment faire pour gérer les comptes de stockage dans Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Comment faire pour définir un abonnement Azure par défaut
Pour gérer le stockage Azure à l’aide de PowerShell d’Azure, vous devez authentifier votre environnement client avec Azure via Azure Active Directory ou l’authentification basée sur certificat. Pour plus d’informations, consultez Didacticiel sur [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Ce guide utilise l’authentification Azure Active Directory.

1.  Dans Windows PowerShell ISE de, tapez la commande suivante pour ajouter votre Azure compte pour l’environnement PowerShell local :

    `Add-AzureAccount`

2.  Dans la fenêtre « Signe dans à Microsoft Azure », tapez l’adresse e-mail et le mot de passe associé à votre compte. Azure authentifie et enregistre les informations d’identification et puis ferme la fenêtre.

3.  Ensuite, exécutez la commande suivante pour afficher les comptes Azure dans votre environnement PowerShell locale et vérifiez que votre compte est répertorié :

    `Get-AzureAccount`

4.  Ensuite, exécutez l’applet de commande suivante pour afficher tous les abonnements qui sont connectés à la session PowerShell locale et vérifiez que votre abonnement est répertorié :

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Pour définir un abonnement Azure par défaut, exécutez l’applet de commande Select-AzureSubscription :

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Vérifiez le nom de l’abonnement par défaut en exécutant la cmdlet Get-AzureSubscription :

    `Get-AzureSubscription -Default`

7.  Pour voir toutes les cmdlets PowerShell disponibles pour le stockage Azure, exécutez :

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Comment faire pour créer un nouveau compte de stockage Azure
Pour utiliser le stockage Azure, vous aurez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour vous connecter à votre abonnement.

1.  Exécutez l’applet de commande Get-AzureLocation pour trouver tous les emplacements de centre de données disponibles :

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Ensuite, exécutez l’applet de commande New-AzureStorageAccount pour créer un nouveau compte de stockage. L’exemple suivant crée un nouveau compte de stockage dans le centre de données « US-ouest ».

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] Le nom de votre compte de stockage doit être unique dans Azure et doit être en minuscule. Pour les conventions d’affectation de noms et les restrictions, reportez-vous à la section [Sur les comptes de stockage Azure](storage-create-storage-account.md) et [d’affectation de nom et référençant des conteneurs, BLOB et les métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Comment configurer un compte de stockage Azure par défaut
Vous pouvez avoir plusieurs comptes de stockage de votre abonnement. Vous pouvez choisir un d'entre eux et le définir comme le compte de stockage par défaut pour toutes les commandes de stockage dans la même session de PowerShell. Cela vous permet d’exécuter les commandes de stockage Azure PowerShell sans spécifier de manière explicite le contexte de stockage.

1.  Pour définir un compte de stockage par défaut pour votre abonnement, vous pouvez exécuter l’applet de commande Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Ensuite, exécutez l’applet de commande Get-AzureSubscription pour vérifier que le compte de stockage est associé à votre compte d’abonnement par défaut. Cette commande renvoie les propriétés d’un abonnement sur l’abonnement en cours, y compris son compte en cours de stockage.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Comment répertorier tous les comptes de stockage Azure dans un abonnement
Chaque abonnement Azure peut avoir jusqu'à 100 comptes de stockage. Pour obtenir les informations les plus récentes sur les limites, consultez [abonnement Azure et les limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).

Exécutez l’applet de commande suivante pour rechercher le nom et l’état des comptes de stockage dans l’abonnement actuel :

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Comment faire pour créer un contexte de stockage Azure
Contexte de stockage Azure est un objet dans PowerShell pour encapsuler les informations d’identification de stockage. À l’aide d’un contexte de stockage lors de l’exécution toute applet de commande suivante vous permet d’authentifier votre requête sans spécifier le compte de stockage et sa touche d’accès rapide d’explicitement. Vous pouvez créer un contexte de stockage de différentes façons, par exemple à l’aide de la clé accès et le nom de compte de stockage, jeton de signature (SAS) d’accès partagé, chaîne de connexion, ou anonyme. Pour plus d’informations, consultez [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilisez une des trois méthodes suivantes pour créer un contexte de stockage :

- Exécutez l’applet de commande [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) pour trouver la clé d’accès de stockage principal pour votre compte de stockage Azure. Ensuite, appeler l’applet de commande [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) pour créer un contexte de stockage :

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Générer un jeton de signature accès partagé pour un conteneur de stockage Azure et l’utiliser pour créer un contexte de stockage :

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Pour plus d’informations, consultez [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) et [L’utilisation partagée accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).

- Dans certains cas, vous souhaiterez spécifier les points de terminaison de service lorsque vous créez un nouveau contexte de stockage. Cela peut être nécessaire lorsque vous avez enregistré un nom de domaine personnalisé pour votre compte de stockage avec le service d’objet Blob ou que vous souhaitez utiliser une signature accès partagé pour accéder à des ressources de stockage. Définir les points de terminaison de service dans une chaîne de connexion et l’utiliser pour créer un nouveau contexte de stockage comme indiqué ci-dessous :

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Pour plus d’informations sur la façon de configurer une chaîne de connexion de stockage, consultez [Configuration des chaînes de connexion](storage-configure-connection-string.md).

Maintenant que vous avez configuré votre ordinateur et appris à gérer les abonnements et les comptes de stockage à l’aide de PowerShell d’Azure, accédez à la section suivante pour savoir comment gérer les objets BLOB Azure et blob des captures instantanées.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Comment faire pour récupérer ou de régénérer les clés de stockage Azure

Un compte de stockage Azure est fourni avec deux clés de compte. Vous pouvez utiliser l’applet de commande suivante pour récupérer vos clés.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Utilisez l’applet de commande suivante pour récupérer une clé spécifique. Les valeurs valides sont primaires et secondaires.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Si vous souhaitez régénérer vos clés, utilisez la cmdlet suivante. Les valeurs valides pour le type de clé - sont « Principal » et « Secondaire »

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>La gestion des objets BLOB Azure
Stockage de Blob Azure est un service permettant de stocker de grandes quantités de données non structurées, telles que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage de Blob Azure. Pour plus d’informations, consultez [mise en route avec le stockage Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et [Concepts de Service d’objet Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Comment faire pour créer un conteneur
Chaque objet blob dans le stockage Azure doit être dans un conteneur. Vous pouvez créer un conteneur privé à l’aide de l’applet de commande New-AzureStorageContainer :

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Il existe trois niveaux d’accès anonyme en lecture : **Off**, **Blob**et un **conteneur**. Pour empêcher l’accès anonyme aux objets BLOB, la valeur du paramètre d’autorisation **Off**. Par défaut, le nouveau conteneur est privé et est accessible uniquement par le propriétaire du compte. Pour autoriser l’accès anonyme en lecture publique à un blob de ressources, mais pas pour les métadonnées de conteneur ou la liste des objets BLOB dans le conteneur, la valeur du paramètre d’autorisation **Blob**. Pour autoriser les accès en lecture publique complète pour le blob de ressources, les métadonnées de conteneur et la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation au **conteneur**. Pour plus d’informations, voir [Gérer les accès en lecture anonymes pour les conteneurs et les objets BLOB](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Comment télécharger un blob dans un conteneur
Stockage de Blob Azure prend en charge les objets BLOB de page et les objets BLOB de bloc. Pour plus d’informations, consultez [Blobs de bloc de compréhension, ajouter un BLOB et BLOB de la Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour charger des objets BLOB sur un conteneur, vous pouvez utiliser l’applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . Par défaut, cette commande transfère les fichiers locaux dans un blob de bloc. Pour spécifier le type de l’objet blob, vous pouvez utiliser le paramètre - BlobType.

L’exemple suivant exécute l’applet de commande [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) pour obtenir tous les fichiers dans le dossier spécifié et les transmet à l’applet de commande suivante à l’aide de l’opérateur de pipeline. L’applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) télécharge les fichiers locaux dans votre conteneur :

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Comment faire pour télécharger des objets BLOB à partir d’un conteneur
L’exemple suivant montre comment télécharger des objets BLOB à partir d’un conteneur. Tout d’abord, l’exemple établit une connexion au stockage Azure en utilisant le contexte de compte de stockage, qui inclut le nom de compte de stockage et de sa clé d’accès principal. Ensuite, l’exemple récupère une référence d’objet blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . Ensuite, l’exemple utilise l’applet de commande [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) pour télécharger des objets BLOB dans le dossier local de destination.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Comment faire pour copier des objets BLOB à partir d’un seul conteneur de stockage à un autre
Vous pouvez copier des objets BLOB entre les régions et les comptes de stockage en mode asynchrone. L’exemple suivant montre comment copier des objets BLOB à partir d’un seul conteneur de stockage à un autre dans les deux comptes de stockage différents. L’exemple affecte d’abord variables pour les comptes de stockage source et de destination et crée ensuite un contexte de stockage pour chaque compte. Ensuite, l’exemple copie BLOB du conteneur source vers le conteneur de destination à l’aide de l’applet de commande [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) . L’exemple suppose que la source et la destination des comptes de stockage et les conteneurs existent déjà.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Notez que cet exemple effectue une copie asynchrone. Vous pouvez surveiller l’état de chaque copie en exécutant l’applet de commande [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Comment faire pour copier des objets BLOB à partir d’un emplacement secondaire
Vous pouvez copier des objets BLOB à partir de l’emplacement secondaire d’un compte activé-RA GRS.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Comment faire pour supprimer un objet blob
Pour supprimer un objet blob, obtenez d’abord une référence d’objet blob et ensuite appeler l’applet de commande Remove-AzureStorageBlob dessus. L’exemple suivant supprime tous les objets BLOB dans un conteneur donné. L’exemple affecte d’abord variables pour un compte de stockage et crée ensuite un contexte de stockage. Ensuite, l’exemple récupère une référence d’objet blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute l’applet de commande [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) pour supprimer les objets BLOB à partir d’un conteneur de stockage Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Comment faire pour gérer les snapshots de blob Azure
Azure permet de créer un instantané d’un objet blob. Un instantané est une version en lecture seule d’un objet blob qui est effectuée à un point dans le temps. Une fois un instantané a été créé, il peut être lue, copié, ou supprimé, mais pas modifié. Les snapshots permettent de sauvegarder un blob tel qu’il apparaît à un moment donné. Pour plus d’informations, consultez [Création d’un instantané d’un objet Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Comment faire pour créer une capture instantanée de blob
Pour créer un instantané d’un objet blob, obtenez d’abord une référence d’objet blob, puis appelez le `ICloudBlob.CreateSnapshot` méthode sur lui. L’exemple suivant définit en premier variables pour un compte de stockage et crée ensuite un contexte de stockage. Ensuite, l’exemple récupère une référence d’objet blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer une capture instantanée.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Comment répertorier les instantanés d’un blob
Vous pouvez créer autant d’instantanés que vous le souhaitez pour un objet blob. Vous pouvez répertorier les instantanés associés le blob pour effectuer le suivi de vos snapshots en cours. L’exemple suivant utilise un objet blob prédéfini et appelle l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) pour répertorier les instantanés de ce blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Comment copier un instantané d’un objet blob
Vous pouvez copier un instantané d’un objet blob de restaurer l’instantané. Pour obtenir des informations détaillées et des restrictions, consultez [Création d’un instantané d’un objet Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). L’exemple suivant définit en premier variables pour un compte de stockage et crée ensuite un contexte de stockage. Ensuite, l’exemple définit les variables de nom conteneur et blob. L’exemple récupère une référence d’objet blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer une capture instantanée. Ensuite, l’exemple exécute l’applet de commande [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) pour copier la capture instantanée d’un objet blob à l’aide de l’objet ICloudBlob pour le blob de la source. Veillez à mettre à jour les variables en fonction de votre configuration avant d’exécuter l’exemple. Notez que l’exemple suivant suppose que la source et les conteneurs de destination et le blob de la source existent déjà.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Maintenant que vous avez appris comment gérer des objets BLOB Azure et blob des snapshots avec PowerShell d’Azure, accédez à la section suivante pour savoir comment gérer les fichiers, les files d’attente et les tables.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Comment faire pour gérer les tables Azure et les entités de table
Service de stockage de Table Azure est un magasin de données NoSQL, ce qui vous permet de stocker et d’interroger des ensembles volumineux de données structurées et non relationnelles. Les principaux composants du service sont les tables, les entités et les propriétés. Une table est une collection d’entités. Une entité est un ensemble de propriétés. Chaque entité peut posséder jusqu'à 252 propriétés, qui sont toutes les paires nom-valeur. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage de Table Azure. Pour plus d’informations, consultez [Présentation du modèle de données de Service de Table](http://msdn.microsoft.com/library/azure/dd179338.aspx) et [mise en route de stockage Azure Table à l’aide de .NET](storage-dotnet-how-to-use-tables.md).

Dans les sous-sections suivantes, vous allez apprendre à gérer le service de stockage Azure Table à l’aide de PowerShell d’Azure. Les scénarios présentés incluent la **Création**, la **suppression**et la **récupération des** **tables**, ainsi que **l’ajout**, **l’interrogation**et **la suppression des entités de la table**.

### <a name="how-to-create-a-table"></a>Comment faire pour créer une table
Chaque table doit résider dans un compte de stockage Azure. L’exemple suivant montre comment créer une table dans le stockage Azure. Tout d’abord, l’exemple établit une connexion au stockage Azure en utilisant le contexte de compte de stockage, qui comprend le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il utilise l’applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) pour créer une table dans le stockage Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Comment faire pour récupérer un tableau
Vous pouvez interroger et extraire une ou toutes les tables dans un compte de stockage. L’exemple suivant montre comment récupérer une table à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Si vous appelez l’applet de commande Get-AzureStorageTable sans aucun paramètre, il obtient toutes les tables de stockage pour un compte de stockage.

### <a name="how-to-delete-a-table"></a>Comment faire pour supprimer une table
Vous pouvez supprimer une table à partir d’un compte de stockage à l’aide de l’applet de commande [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Comment faire pour gérer les entités de table
Actuellement, Azure PowerShell ne fournit pas d’applets de commande pour gérer les entités de table directement. Pour effectuer des opérations sur les entités de table, vous pouvez utiliser les classes dans la [Bibliothèque de Client de stockage Azure pour .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Comment faire pour ajouter des entités de table
Pour ajouter une entité à une table, commencez par créer un objet qui définit les propriétés d’entité. Une entité peut avoir jusqu'à 255 propriétés, y compris les 3 propriétés du système : **PartitionKey**, **RowKey**et **Timestamp**. Vous êtes chargé d’insertion et de mise à jour des valeurs de **PartitionKey** et **RowKey**. Le serveur gère la valeur de l' **horodatage**, qui ne peut pas être modifié. Ensemble les **PartitionKey** et **RowKey** identifient chaque entité au sein d’une table.

-   **PartitionKey**: détermine que l’entité est stockée dans la partition.
-   **RowKey**: identifie de façon unique l’entité au sein de la partition.

Vous pouvez définir des propriétés personnalisées jusqu'à 252 pour une entité. Pour plus d’informations, consultez [Présentation du modèle de données de Service de Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

L’exemple suivant montre comment ajouter des entités à un tableau. L’exemple montre comment récupérer la table des employés et d’intégrer plusieurs entités. Tout d’abord, il établit une connexion au stockage Azure en utilisant le contexte de compte de stockage, qui comprend le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il récupère la table donnée à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la table n’existe pas, l’applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) est utilisé pour créer une table dans le stockage Azure. Ensuite, l’exemple définit une fonction personnalisée Ajouter-entité à ajouter des entités à la table en spécifiant la clé de ligne et de la partition de chaque entité. La fonction Ajouter-entité appelle l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) sur la classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) pour créer un objet d’entité. Ensuite, l’exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) sur cet objet d’entité à ajouter à une table.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Comment faire pour interroger les entités de table
Pour interroger une table, utilisez la classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . L’exemple suivant suppose que vous avez déjà exécuté le script indiqué dans la procédure pour ajouter la section entités de ce guide. Tout d’abord, l’exemple établit une connexion au stockage Azure en utilisant le contexte de stockage, ce qui inclut le nom de compte de stockage et de sa clé d’accès. Ensuite, il tente de récupérer la table « Employés » créée précédemment à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . L’appel de l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) sur la classe Microsoft.WindowsAzure.Storage.Table.TableQuery crée un nouvel objet de la requête. L’exemple recherche les entités qui ont une colonne 'ID' dont la valeur est 1, comme spécifié dans un filtre de chaîne. Pour plus d’informations, consultez [requêtes des Tables et des entités](http://msdn.microsoft.com/library/azure/dd894031.aspx). Lorsque vous exécutez cette requête, il renvoie toutes les entités qui correspondent aux critères de filtre.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Comment faire pour supprimer des entités de table
Vous pouvez supprimer une entité à l’aide de ses clés de partition et de ligne. L’exemple suivant suppose que vous avez déjà exécuté le script indiqué dans la procédure pour ajouter la section entités de ce guide. Tout d’abord, l’exemple établit une connexion au stockage Azure en utilisant le contexte de stockage, ce qui inclut le nom de compte de stockage et de sa clé d’accès. Ensuite, il tente de récupérer la table « Employés » créée précédemment à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la table existe, l’exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) pour récupérer une entité en fonction de ses valeurs de clé de partition et de ligne. Puis, passez l’entité à la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) pour supprimer.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Comment faire pour gérer les files d’attente Azure et les messages de la file d’attente
Stockage Azure de file d’attente est un service permettant de stocker de grandes quantités de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage Azure file d’attente. Pour plus d’informations, consultez [mise en route de stockage de la file d’attente de Azure à l’aide de .NET](storage-dotnet-how-to-use-queues.md).

Cette section vous montrera comment gérer le service de stockage de file d’attente d’Azure à l’aide de PowerShell d’Azure. Les scénarios présentés incluent **l’Insertion** et **suppression** de file d’attente messages, ainsi que **Création**, **suppression**et **extraction des files d’attente**.

### <a name="how-to-create-a-queue"></a>Comment faire pour créer une file d’attente
L’exemple suivant établit d’abord une connexion au stockage Azure en utilisant le contexte de compte de stockage, qui comprend le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il appelle la cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) pour créer une file d’attente nommée 'nom'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Pour plus d’informations sur les conventions d’affectation de noms pour le Service de file d’attente d’Azure, consultez [métadonnées et attribution de noms de files d’attente](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Comment faire pour extraire une file d’attente
Vous pouvez interroger et extraire une file d’attente spécifique ou une liste de toutes les files d’attente dans un compte de stockage. L’exemple suivant montre comment récupérer une file d’attente spécifiée à l’aide de l’applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Si vous appelez l’applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sans aucun paramètre, il obtient une liste de toutes les files d’attente.

### <a name="how-to-delete-a-queue"></a>Comment faire pour supprimer une file d’attente
Pour supprimer une file d’attente et tous les messages qu’il contient, appeler l’applet de commande Remove-AzureStorageQueue. L’exemple suivant montre comment supprimer une file d’attente spécifiée à l’aide de l’applet de commande Remove-AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Insertion d’un message dans une file d’attente
Pour insérer un message dans une file d’attente existante, commencez par créer une nouvelle instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Ensuite, appelez la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Un CloudQueueMessage peut être créée à partir d’une chaîne (au format de format UTF-8) ou un tableau d’octets.

L’exemple suivant montre comment ajouter un message à une file d’attente. Tout d’abord, l’exemple établit une connexion au stockage Azure en utilisant le contexte de compte de stockage, qui comprend le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il récupère de la file d’attente spécifiée à l’aide de l’applet de commande [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Si la file d’attente existe, l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) est utilisé pour créer une instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Ensuite, l’exemple appelle la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) sur cet objet de message pour l’ajouter à une file d’attente. Voici le code qui extrait une file d’attente et le message 'MessageInfo' :

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>La file d’attente le message suivant
Votre code des files d’attente de message d’une file d’attente en deux étapes. Lorsque vous appelez la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , vous obtenez le message suivant dans une file d’attente. Un message retourné à partir de **GetMessage** devienne invisible pour un autre code de lecture des messages de cette file d’attente. Pour terminer la suppression du message de la file d’attente, vous devez également appeler la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Ce processus en deux étapes de suppression d’un message permet de garantir que si votre code ne parvient pas à traiter un message en raison d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et essayez à nouveau. Votre code appelle **DeleteMessage** droite une fois que le message a été traité.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Comment faire pour gérer les fichiers et les partages de fichiers Azure
Stockage de fichier Azure offre un stockage partagé pour les applications utilisant le protocole SMB standard. Ordinateurs virtuels Microsoft Azure et les services en nuage peuvent partager des données entre les composants d’application via les partages montés, et les applications sur site peuvent accéder à des données de fichier dans un partage via l’API de stockage de fichiers ou l’Azure PowerShell.

Pour plus d’informations sur le stockage de fichiers d’Azure, consultez [mise en route de stockage Azure sur Windows](storage-dotnet-how-to-use-files.md) et les [API de fichier Service REST](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Comment définir et interroger analytique de stockage
[Analytique de stockage Azure](storage-analytics.md) permet de collecter des mesures pour vos comptes de stockage Azure et les données du journal sur les demandes envoyées à votre compte de stockage. Vous pouvez utiliser des mesures de stockage pour surveiller l’état d’un compte de stockage et lors de la journalisation de stockage pour diagnostiquer et résoudre les problèmes liés à votre compte de stockage.
Par défaut, les mesures de stockage ne sont pas activé pour les services de stockage. Vous pouvez activer la surveillance de l’utilisation de Windows PowerShell ou Azure Portal, ou par programme à l’aide de la bibliothèque de client de stockage. Enregistrement de stockage produit côté serveur et vous permet d’enregistrer les détails pour les réussites et les échecs des demandes dans votre compte de stockage. Ces journaux permettre de voir les détails de lire, d’écrire et de supprimer des opérations sur vos tables, files d’attente et BLOB ainsi que les raisons de demandes ayant échoué.

Pour savoir comment activer et afficher les données de métrique du stockage à l’aide de PowerShell, voir [comment activer les indicateurs de stockage à l’aide de PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Pour savoir comment activer et récupérer les données de l’enregistrement de stockage à l’aide de PowerShell, voir [comment activer la journalisation de l’utilisation de PowerShell de stockage](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) et la [recherche de vos données de journal journalisation de stockage](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Pour plus d’informations sur l’utilisation des indicateurs de stockage et de la journalisation de stockage pour résoudre les problèmes de stockage, reportez-vous à la section [surveillance, de diagnostic et résolution des problèmes Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Comment gérer la Signature de l’accès partagé (SAS) et de la stratégie d’accès stockées
Accès partagé signatures constituent une partie importante du modèle de sécurité de toute application utilisant le stockage Azure. Ils sont utiles pour fournir des autorisations limitées à votre compte de stockage pour les clients n’ayant pas la clé de compte. Par défaut, seul le propriétaire du compte de stockage peut-être accéder BLOB, les tables et les files d’attente dans ce compte. Si votre service ou votre application a besoin libérer ces ressources à d’autres clients sans partager votre clé d’accès, vous disposez de trois options :

- Définir les autorisations d’un conteneur pour autoriser l’accès anonyme en lecture au conteneur et à ses objets BLOB. Ce n’est pas autorisé pour les tables ou les files d’attente.
- Utiliser une signature d’accès partagé qu’accorde les droits d’accès pour les conteneurs, les blobs, les files d’attente et les tables de restreints pour un intervalle de temps spécifique.
- Utiliser une stratégie d’accès stockée pour obtenir un niveau supplémentaire de contrôle sur les signatures d’accès partagé pour un conteneur ou ses objets BLOB, une file d’attente ou d’une table. La stratégie d’accès stockée vous permet de modifier l’heure de début, délai d’expiration ou les autorisations pour une signature ou pour révoquer il après qu’il a été délivré.

Une signature accès partagé peut être l’une des deux formes :

- **SAS ad hoc**: lorsque vous créez un SAS ad hoc, l’heure de début, l’heure d’expiration, et que les autorisations pour les associations de sécurité sont spécifiées sur l’URI de SAS. Ce type d’associations de sécurité peut-être être créé sur un conteneur, le blob, table, ou file d’attente et qu’il est non revokable.
- **Associations de sécurité avec la stratégie d’accès stockée**: une stratégie d’accès stockée est définie sur un conteneur de conteneur d’un blob de ressources, d’une table ou d’une file d’attente -, et vous pouvez l’utiliser pour gérer les contraintes pour une ou plusieurs signatures d’accès partagé. Lorsque vous associez un SAS à une stratégie d’accès stockée, les associations de sécurité hérite les contraintes - l’heure de début, date d’expiration et les autorisations - définies pour la stratégie d’accès stockée. Ce type d’associations de sécurité est revokable.

Pour plus d’informations, reportez-vous [à l’aide de partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) et [gérer l’accès en lecture anonyme à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md).

Dans les sections suivantes, vous apprendrez comment créer une stratégie d’accès stockée et de jeton de signature accès partagé pour les tables Azure. PowerShell Azure fournit des applets de commande similaires pour les conteneurs, les BLOB et les files d’attente ainsi. Pour exécuter les scripts dans cette section, téléchargez l' [Azure PowerShell version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou une version ultérieure.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Comment faire pour créer un jeton de Signature de l’accès partagé basée sur des règles
Pour créer une nouvelle stratégie d’accès stockées, utilisez l’applet de commande New-AzureStorageTableStoredAccessPolicy. Ensuite, appeler l’applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un nouveau jeton de signature basée sur une stratégie d’accès partagé pour une table de stockage Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Comment faire pour créer un jeton de Signature de l’accès partagé ad hoc (non-revokable)
Utilisez l’applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un nouveau jeton de Signature de l’accès partagé de (non-revokable) ad hoc pour une table de stockage Azure :

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Comment faire pour créer une stratégie d’accès stockée
Utilisez l’applet de commande New-AzureStorageTableStoredAccessPolicy pour créer une nouvelle stratégie d’accès stockée pour une table de stockage Azure :

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>La mise à jour d’une stratégie d’accès stockée
L’applet de commande Set-AzureStorageTableStoredAccessPolicy permet de mettre à jour d’une stratégie d’accès stockée existant d’une table de stockage Azure :

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Comment supprimer une stratégie d’accès stockée
Pour supprimer une stratégie d’accès stockées dans une table de stockage Azure, utilisez l’applet de commande Remove-AzureStorageTableStoredAccessPolicy :

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Comment faire pour utiliser le stockage Azure pour le gouvernement des États-Unis et la Chine d’Azure
Un environnement Azure est un déploiement indépendant de Microsoft Azure, comme [AzureChinaCloud pour Azure exploité par 21Vianet en Chine](http://www.windowsazure.cn/), [AzureCloud pour Azure global](https://portal.azure.com)et [Gouvernement Azure pour le gouvernement des États-Unis](https://azure.microsoft.com/features/gov/). Vous pouvez déployer de nouveaux environnements Azure pour le gouvernement des États-Unis et la Chine d’Azure.

Pour utiliser le stockage Azure avec AzureChinaCloud, vous devez créer un contexte de stockage associé à AzureChinaCloud. Suivez ces étapes pour vous aider à démarrer :

1.  Exécutez l’applet de commande [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) pour afficher les disponibilité des environnements Azure :

    `Get-AzureEnvironment`

2.  Ajouter un compte Azure Chine à Windows PowerShell :

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Créer un contexte de stockage pour un compte de AzureChinaCloud :

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Pour utiliser le stockage Azure avec [Azure du gouvernement américain](https://azure.microsoft.com/features/gov/), vous devez définir un nouvel environnement et ensuite créer un nouveau contexte de stockage avec cet environnement :

1.  Exécutez l’applet de commande [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) pour afficher les disponibilité des environnements Azure :

    `Get-AzureEnvironment`

2.  Ajouter un compte Azure américains à Windows PowerShell :

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Créer un contexte de stockage pour un compte de AzureUSGovernment :

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Pour plus d’informations, voir :

- [Guide du développeur Microsoft Azure gouvernement](../azure-government-developer-guide.md).
- [Vue d’ensemble des différences lors de la création d’une Application de Service de la Chine](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide, vous avez appris comment gérer le stockage Azure avec PowerShell d’Azure. Voici certains articles connexes et des ressources pour en savoir plus à leur sujet.

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Les applets de commande PowerShell stockage Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Référence de Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
