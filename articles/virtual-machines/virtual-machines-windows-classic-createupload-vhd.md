<properties
    pageTitle="Créer et télécharger une image de machine virtuelle à l’aide de Powershell | Microsoft Azure"
    description="Apprenez à créer et télécharger une image Windows Server généralisée (VHD) à l’aide du modèle de déploiement classique et Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Créer et télécharger un disque dur virtuel du serveur de Windows Azure

Cet article vous montre comment télécharger votre propre image généralisée de la machine virtuelle sous la forme d’un disque dur virtuel (VHD) qui vous permet de créer des machines virtuelles. Pour plus d’informations sur les disques et les disques durs virtuels dans Microsoft Azure, reportez-vous à la section [sur les disques et les disques durs virtuels pour les Machines virtuelles](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Vous pouvez également [Télécharger](virtual-machines-windows-upload-image.md) une machine virtuelle en utilisant le modèle de gestionnaire de ressources. 

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez :

- **Abonnement d’un Azure** - si vous n’en avez pas, vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - vous avez le module PowerShell de Microsoft Azure, installé et configuré pour utiliser votre abonnement. 

- **A . Fichier de disque dur virtuel** - stockées dans un fichier .vhd et attaché à une machine virtuelle de système d’exploitation de Windows prises en charge. Vérifier si les rôles de serveur en cours d’exécution sur le disque dur virtuel sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] Le format VHDX n’est pas pris en charge dans Microsoft Azure. Vous pouvez convertir le disque au format de disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l' [applet de commande Convert-disque dur virtuel](http://technet.microsoft.com/library/hh848454.aspx). Pour plus d’informations, consultez cette [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Étape 1 : Préparer le disque dur virtuel 

Avant de télécharger le disque dur virtuel sur Azure, il doit être généralisé à l’aide de l’outil Sysprep. Cela prépare le disque dur virtuel à utiliser en tant qu’image. Pour plus d’informations sur Sysprep, consultez [Comment faire pour utiliser Sysprep : An Introduction](http://technet.microsoft.com/library/bb457073.aspx). Sauvegarder la machine virtuelle avant d’exécuter Sysprep.

À partir de l’ordinateur virtuel qui a été installé le système d’exploitation, procédez comme suit :

1. Ouvrez une session sur le système d’exploitation.

2. En tant qu’administrateur, ouvrez une fenêtre d’invite de commande. Modifiez le répertoire **%windir%\system32\sysprep**et exécutez `sysprep.exe`.

    ![Ouvrez une fenêtre d’invite de commandes](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  La boîte de dialogue **Outil de préparation système** s’affiche.

    ![Démarrez Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Dans l' **Outil de préparation système**, sélectionnez **Entrer système de première expérience (OOBE)** et assurez-vous que l’option **Generalize** est activée.

5.  Dans les **Options d’arrêt**, sélectionnez **Arrêter le système**.

6.  Cliquez sur **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Étape 2 : Créer un compte de stockage et un conteneur

Vous avez besoin d’un compte de stockage dans Azure afin d’avoir un emplacement pour télécharger le fichier .vhd. Cette étape vous montre comment créer un compte, ou obtenir les informations dont vous avez besoin d’un compte existant. Remplacez les variables de &lsaquo; crochets &rsaquo; avec vos propres informations.

1. Ouverture de session

        Add-AzureAccount

1. Définissez votre abonnement Azure.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Créer un nouveau compte de stockage. Le nom du compte de stockage doit être unique, 3-24 caractères. Le nom peut être n’importe quelle combinaison de lettres et de chiffres. Vous devez également spécifier un emplacement comme « East nous »
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Définir le nouveau compte de stockage par défaut.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Créer un nouveau conteneur.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Étape 3 : Télécharger le fichier .vhd

L' [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) permet de télécharger le disque dur virtuel.

Dans la fenêtre PowerShell d’Azure que vous avez utilisé à l’étape précédente, tapez la commande suivante et remplacez les variables de &lsaquo; crochets &rsaquo; avec vos propres informations.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Étape 4 : Ajouter l’image à la liste des images personnalisées

Utilisez l’applet de commande [Add-AzureVMImage](https://msdn.microsoft.com/library/mt589167.aspx) pour ajouter l’image à la liste de vos images personnalisées.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez à présent [créer un ordinateur virtuel personnalisé](virtual-machines-windows-classic-createportal.md) à l’aide de l’image que vous avez téléchargé.

