<properties
    pageTitle="Créer un ordinateur virtuel de SQL Server dans PowerShell Azure (standard) | Microsoft Azure"
    description="Fournit des étapes et des scripts PowerShell pour la création d’une machine virtuelle d’Azure avec des images de la galerie des machines virtuelles de SQL Server. Cette rubrique utilise le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Mettre en service un ordinateur virtuel de SQL Server à l’aide de PowerShell d’Azure (classique)

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment créer un ordinateur virtuel de SQL Server dans Azure à l’aide des applets de commande PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour la version du Gestionnaire de ressources de cette rubrique, consultez [fourniture d’une machine virtuelle de SQL Server à l’aide du Gestionnaire de ressources de PowerShell Azure](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installation et configuration de PowerShell :

1. Si vous ne disposez pas d’un compte Azure, visitez [Azure version d’essai gratuite](https://azure.microsoft.com/pricing/free-trial/).

2. [Téléchargez et installez les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md).

3. Démarrez Windows PowerShell et connectez-le à votre abonnement Azure avec la commande **Add-AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Déterminer votre cible région Azure

Votre Machine virtuelle de SQL Server seront hébergés dans un service en nuage qui se trouve une zone Azure. Les étapes suivantes vous aideront à déterminer votre région, le compte de stockage et cloud service qui sera utilisé pour le reste de ce didacticiel.

1. Déterminer le centre de données que vous souhaitez utiliser pour héberger votre ordinateur virtuel de SQL Server. Les commandes PowerShell suivantes affiche les zones disponibles en détail avec une liste récapitulative à la fin.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Une fois que vous avez identifié l’emplacement par défaut, définissez une variable nommée **$dcLocation** à cette région.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurez votre compte de stockage et d’abonnement

1. Déterminez l’abonnement Azure que vous utiliserez pour le nouvel ordinateur virtuel.

        (Get-AzureSubscription).SubscriptionName

1. Affecter votre abonnement Azure de la cible à la variable **$subscr** . Définissez en tant que votre abonnement Azure.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Vérifiez ensuite pour les comptes de stockage existants. Le script suivant affiche tous les comptes de stockage qui existent dans votre région choisie :

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Si vous avez besoin d’un nouveau compte de stockage, tout d’abord créer un nom de compte de stockage de toutes les minuscules avec la commande New-AzureStorageAccount, comme dans l’exemple suivant : **Nouveau-AzureStorageAccount - StorageAccountName "<storage account name>"-emplacement $dcLocation**

1. Affecter le nom de compte de stockage cible de la **$staccount**. Puis utilisez **Set-AzureSubscription** pour définir l’abonnement et le compte de stockage actuel.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Sélectionnez une image de machine virtuelle de SQL Server

1. Découvrez la liste des images de machines virtuelles de SQL Server disponibles à partir de la galerie. Ces images qu'ont tous une propriété **ImageFamily** qui commence par « SQL ». La requête suivante affiche la famille d’image disponible à vous qui avez SQL Server préinstallé.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Lorsque vous trouvez la famille d’image de machine virtuelle, il peut exister plusieurs images publiées dans cette famille. Pour trouver le nom d’image VM publié plus récent pour votre famille image sélectionnée (par exemple, **SQL Server 2016 RTM Enterprise de Windows Server 2012 R2**), utilisez le script suivant :

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Créer l’ordinateur virtuel

Enfin, créez l’ordinateur virtuel avec PowerShell :

1. Créer un service en nuage pour héberger la nouvelle machine virtuelle. Notez qu’il est également possible d’utiliser un service cloud existant à la place. Créez une variable nouvelle **$svcname** avec le nom court du service nuage.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Spécifiez le nom de la machine virtuelle et une taille. Pour plus d’informations sur les tailles de la machine virtuelle, voir [Taille de Machine virtuelle pour Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Spécifiez le compte d’administrateur local et d’un mot de passe.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Exécutez le script suivant pour créer la machine virtuelle.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Pour des explications supplémentaires et des options de configuration, reportez-vous à la section **créer votre jeu de commandes** de [PowerShell d’Azure utilisation permet de créer et de préconfigurer des Machines virtuelles basées sur Windows](virtual-machines-windows-classic-create-powershell.md).

## <a name="example-powershell-script"></a>Exemple de script PowerShell

Le script suivant fournit et l’exemple d’un script complet qui crée un ordinateur virtuel de **SQL Server 2016 RTM Enterprise de Windows Server 2012 R2** . Si vous utilisez ce script, vous devez personnaliser les variables initiales selon les étapes précédentes de cette rubrique.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Connexion Bureau à distance

1. Créer le. Fichiers RDP dans le dossier documents de l’utilisateur actuel pour lancer ces ordinateurs virtuels pour terminer l’installation :

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Dans le répertoire documents, lancez le fichier RDP. Se connecter avec le nom d’utilisateur administrateur et le mot de passe fourni plus haut (par exemple, si votre nom d’utilisateur a été VMAdmin, spécifiez « \VMAdmin » en tant que l’utilisateur et le mot de passe).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Terminer la configuration de l’ordinateur SQL Server pour l’accès distant

Après l’ouverture de session sur l’ordinateur de bureau à distance, configurer SQL Server selon les instructions dans les [étapes de configuration de la connectivité de SQL Server dans un ordinateur virtuel d’Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des instructions supplémentaires pour le provisionnement des machines virtuelles avec PowerShell dans la [documentation de machines virtuelles](virtual-machines-windows-classic-create-powershell.md). Pour les scripts supplémentaires liés à SQL Server et le stockage de la prime, voir [Utiliser le stockage Azure Premium avec SQL Server sur des Machines virtuelles](virtual-machines-windows-classic-sql-server-premium-storage.md).

Dans de nombreux cas, l’étape suivante consiste à migrer vos bases de données de cette nouvelle VM de SQL Server. Pour instructions de migration de base de données, reportez-vous à la section [migration d’une base de données SQL Server sur un ordinateur virtuel d’Azure](virtual-machines-windows-migrate-sql.md).

Si vous souhaitez également dans le portail Azure pour créer des Machines virtuelles de SQL, reportez-vous à la section [mise en service d’une Machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md). Notez que le didacticiel qui vous guide à travers le portail crée des machines virtuelles à l’aide du Gestionnaire de ressources de modèle recommandé, plutôt que le modèle classique utilisé dans cette rubrique de PowerShell.

En plus de ces ressources, nous vous recommandons de passer en revue les [autres rubriques relatives à l’exécution de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
