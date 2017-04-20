<properties
   pageTitle="Extension de Script personnalisée sur un ordinateur virtuel de Windows | Microsoft Azure"
   description="Automatiser les tâches de configuration Azure VM à l’aide de l’extension d’un Script personnalisé pour exécuter des scripts PowerShell sur un ordinateur virtuel de Windows à distance"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extension de Script personnalisée pour les machines virtuelles Windows

Cet article donne une vue d’ensemble de l’utilisation de l’extension Script personnalisé sur les ordinateurs virtuels de Windows à l’aide des applets de commande PowerShell de Azure avec les API de gestion de Service Azure.

Extensions de la machine virtuelle (VM) sont créées par Microsoft et trusted éditeurs tiers pour étendre les fonctionnalités de la machine virtuelle. Pour une vue d’ensemble des extensions de la machine virtuelle, consultez [fonctionnalités et extensions de Azure VM](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [exécuter ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Vue d’ensemble des extension de Script personnalisé

Avec l’extension de Script personnalisé pour Windows, vous pouvez exécuter des scripts PowerShell sur un ordinateur virtuel à distance sans vous connecter à celle-ci. Vous pouvez exécuter les scripts après la mise en service de la machine virtuelle, ou à tout moment pendant le cycle de vie de la machine virtuelle sans ouvrir de ports supplémentaires. Les cas d’usage plus courants pour l’exécution d’un Script personnalisé extension sont en cours d’exécution, l’installation et la configuration des logiciels supplémentaires sur l’ordinateur virtuel après que qu’il est mis en service.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Conditions requises pour l’exécution de l’extension de Script personnalisé

1. Installer les <a href="http://azure.microsoft.com/downloads" target="_blank">applets de commande PowerShell de Azure</a> version 0.8.0 ou une version ultérieure.
2. Si vous souhaitez que les scripts à exécuter sur un ordinateur virtuel existant, assurez-vous de Qu'agent de la machine virtuelle est activé sur l’ordinateur virtuel. S’il n’est pas installé, procédez comme [suit](virtual-machines-windows-classic-agents-and-extensions.md). Si la machine virtuelle est créée à partir du portail Azure, Agent de la machine virtuelle est installé par défaut.
3. Télécharger les scripts que vous souhaitez exécuter sur l’ordinateur virtuel pour le stockage Azure. Les scripts peuvent provenir d’un seul conteneur ou de plusieurs conteneurs de stockage.
4. Le script doit être créé afin que le script d’entrée, qui est démarré par l’extension, démarre les autres scripts.

## <a name="custom-script-extension-scenarios"></a>Scénarios d’extension de Script personnalisés

### <a name="upload-files-to-the-default-container"></a>Télécharger des fichiers vers le conteneur par défaut

L’exemple suivant montre comment vous pouvez exécuter vos scripts sur l’ordinateur virtuel s’ils sont dans le conteneur de stockage du compte par défaut de votre abonnement. Vous téléchargez vos scripts dans ContainerName. Vous pouvez vérifier le compte de stockage par défaut à l’aide de la **Get-AzureSubscription – par défaut** commande.

L’exemple suivant crée un ordinateur virtuel, mais vous pouvez également exécuter le même scénario sur un ordinateur virtuel existant.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Télécharger des fichiers dans un conteneur de stockage des autres que par défaut

Ce scénario montre comment utiliser un conteneur de stockage des autres que par défaut l’abonnement même ou dans un autre abonnement pour le téléchargement de scripts et fichiers. Cet exemple montre un ordinateur virtuel existant, mais les mêmes opérations peuvent être effectuées lorsque vous créez un ordinateur virtuel.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Scripts de téléchargement à plusieurs conteneurs entre les comptes de stockage différents

  Si les fichiers de script sont stockées sur plusieurs conteneurs, vous devez fournir l’URL de signatures (SAS) de plein accès partagé pour les fichiers pour exécuter les scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Ajouter l’extension de Script personnalisé à partir du portail Azure

Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Azure</a> et ajouter l’extension en spécifiant le fichier de script à exécuter.

  ![Spécifier le fichier de script][5]


### <a name="uninstall-the-custom-script-extension"></a>Désinstaller l’extension de Script personnalisé

Vous pouvez désinstaller l’extension de Script personnalisé à partir de la machine virtuelle à l’aide de la commande suivante.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Utiliser l’extension de Script personnalisé avec les modèles

Pour en savoir plus sur l’utilisation de l’extension Script personnalisé avec les modèles du Gestionnaire de ressources Azure, consultez [à l’aide de l’extension d’un Script personnalisé pour les machines virtuelles de Windows avec des modèles d’Azure le Gestionnaire de ressources](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
