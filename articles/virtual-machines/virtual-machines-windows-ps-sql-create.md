<properties
    pageTitle="Créer un ordinateur virtuel de SQL Server dans PowerShell Azure (responsable de ressources) | Microsoft Azure"
    description="Fournit des étapes et des scripts PowerShell pour la création d’une machine virtuelle d’Azure avec des images de la galerie des machines virtuelles de SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Mettre en service un ordinateur virtuel de SQL Server à l’aide de PowerShell d’Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Portail](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment créer une seule machine virtuelle Azure en utilisant le modèle de déploiement **d’Azure le Gestionnaire de ressources** à l’aide des applets de commande PowerShell d’Azure. Dans ce didacticiel, nous allons créer une seule machine virtuelle à l’aide d’un seul lecteur de disque à partir d’une image dans la galerie de SQL. Nous allons créer de nouveaux fournisseurs pour le stockage, réseau et les ressources de calcul qui seront utilisés par l’ordinateur virtuel. Si vous avez des fournisseurs existants pour une de ces ressources, vous pouvez utiliser ces fournisseurs à la place.

Si vous avez besoin de la version classique de cette rubrique, consultez [fourniture d’une machine virtuelle de SQL Server à l’aide d’Azure PowerShell classique](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Conditions préalables

Pour ce didacticiel, vous devez :

- Un compte Azure et votre abonnement avant de commencer. Si vous n’en avez pas, inscrivez-vous pour une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), minimale 1.4.0 ou version ultérieure (ce didacticiel sont écrit à l’aide de la version 1.5.0).
    - Pour obtenir votre version, tapez **Get-Module Azure-ListAvailable**.

## <a name="configure-your-subscription"></a>Configurer votre abonnement

Ouvrir Windows PowerShell et à établir un accès à votre compte Azure en exécutant la cmdlet suivante. Un écran de connexion pour entrer vos informations d’identification s’affiche. Utilisez le même e-mail et mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Une fois connecté avec succès, vous verrez certaines informations sur l’écran qui comprend le SubscriptionId avec lequel vous connecté. Il s’agit de l’abonnement dans lequel les ressources de ce didacticiel seront créés, sauf si vous modifiez à un autre abonnement. Si vous avez plusieurs SubscriptionIds, exécutez l’applet de commande suivant pour retourner une liste de tous vos SubscriptionIds :

    Get-AzureRmSubscription

Pour passer à un autre SubscriptionID, exécutez l’applet de commande suivant avec votre SubscriptionId souhaité.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Définir des variables de l’image

Pour simplifier l’utilisation et la compréhension du script terminée à partir de ce didacticiel, nous allons commencer par définir un certain nombre de variables. Modifier les valeurs de paramètre que vous souhaitez mais Méfiez-vous des restrictions liées à la longueur des noms et des caractères spéciaux lorsque vous modifiez les valeurs fournies d’attribution de noms.

### <a name="location-and-resource-group"></a>Emplacement et groupe de ressources
Utiliser deux variables pour définir la région de données et le groupe de ressources dans lequel vous allez créer les autres ressources de l’ordinateur virtuel.

Modifier comme vous le souhaitez, puis exécutez les applets de commande suivantes pour initialiser ces variables.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propriétés de stockage

Utilisez les variables suivantes pour définir le compte de stockage et le type de stockage à utiliser par la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivant pour initialiser ces variables. Notez que dans cet exemple, nous utilisons le [Stockage de prime](../storage/storage-premium-storage.md), est recommandé pour les charges de travail. Pour plus d’informations sur ce guide et les autres recommandations, voir [méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propriétés de réseau

Utilisez les variables suivantes pour définir l’interface réseau, le mode de répartition de TCP/IP, le nom de réseau virtuel, le nom virtuel de sous-réseau, la plage d’adresses IP pour le réseau virtuel, la plage d’adresses IP pour le sous-réseau et l’étiquette du nom de domaine public pour être utilisés par le réseau de la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivant pour initialiser ces variables.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Propriétés de la machine virtuelle

Utilisez les variables suivantes pour définir le nom de l’ordinateur virtuel, le nom de l’ordinateur, la taille de la machine virtuelle et le nom du disque système d’exploitation de l’ordinateur virtuel.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivant pour initialiser ces variables.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propriétés de l’image

Utilisez les variables suivantes pour définir l’image à utiliser pour la machine virtuelle. Dans cet exemple, l’image de l’entreprise de 2016 de SQL Server est utilisée.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivant pour initialiser ces variables.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Notez que vous pouvez obtenir une liste complète des offres d’image de SQL Server avec la commande Get-AzureRmVMImageOffer :

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Et vous pouvez visualiser les références SKU disponibles pour une offre avec la commande Get-AzureRmVMImageSku. La commande suivante affiche que toutes les références disponibles pour le **SQL2014SP1-WS2012R2** offrent.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avec le modèle de déploiement Gestionnaire de ressources, le premier objet que vous créez est le groupe de ressources. L’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) nous permet de créer un groupe de ressources Azure et de ses ressources avec le nom du groupe de ressources et l’emplacement défini par les variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer votre nouveau groupe de ressources.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Créer un compte de stockage

L’ordinateur virtuel requiert des ressources de stockage pour le disque du système d’exploitation et les fichiers journaux et de données de SQL Server. Pour plus de simplicité, nous allons créer un seul disque pour les deux. Vous pouvez attacher des disques supplémentaires ultérieurement à l’aide de l’applet de commande [Add-Azure disque](https://msdn.microsoft.com/library/azure/dn495252.aspx) afin de placer vos données SQL Server et les journaux sur des disques dédiés. L’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) nous permet de créer un compte de stockage standard dans votre nouveau groupe de ressources et avec le nom du compte de stockage, le nom de référence de stockage et l’emplacement défini à l’aide de variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer un nouveau compte de stockage.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Créer des ressources de réseau

La machine virtuelle nécessite un certain nombre de ressources réseau pour les connexions réseau.

- Chaque machine virtuelle nécessite un réseau virtuel.
- Un réseau virtuel doit avoir au moins un sous-réseau défini.
- Une interface réseau doit être définie avec une adresse IP privée ou publique.

### <a name="create-a-virtual-network-subnet-configuration"></a>Créer une configuration de sous-réseau du réseau virtuel

Nous allons commencer par créer une configuration de sous-réseau pour notre réseau virtuel. Pour notre didacticiel, nous allons créer un sous-réseau par défaut à l’aide de l’applet de commande [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . Nous allons créer notre configuration de sous-réseau du réseau virtuel avec le préfixe de sous-réseau nom et l’adresse défini en utilisant les variables que vous avez déjà initialisé.

>[AZURE.NOTE] Vous pouvez définir des propriétés supplémentaires de la configuration de sous-réseau de réseau virtuel à l’aide de cette applet de commande, mais qui n’entre pas dans le cadre de ce didacticiel.

Exécutez l’applet de commande suivante pour créer la configuration de votre sous-réseau virtuel.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Ensuite, nous allons créer notre réseau virtuel à l’aide de l’applet de commande [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) . Nous allons créer notre réseau virtuel dans votre nouveau groupe de ressources, avec les nom, emplacement et adresse le préfixe défini à l’aide de variables que vous avez déjà initialisé et à l’aide de la configuration de sous-réseau que vous avez défini à l’étape précédente.

Exécutez l’applet de commande suivante pour créer votre réseau virtuel.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Créer l’adresse IP publique

Maintenant que nous avons notre réseau virtuel défini, nous devons configurer une adresse IP pour la connectivité à l’ordinateur virtuel. Pour ce didacticiel, nous allons créer une adresse IP publique à l’aide de pour prendre en charge de la connectivité Internet d’adressage IP dynamique. Nous utiliserons l’applet de commande [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) pour créer l’adresse IP publique, dans le groupe de ressources créé prevously et avec le nom, emplacement, méthode de répartition et étiquette de nom de domaine DNS défini à l’aide de variables que vous avez déjà initialisé.

>[AZURE.NOTE] Vous pouvez définir d’autres propriétés de l’adresse IP publique à l’aide de cette applet de commande, mais qui est au-delà de la portée de ce didacticiel initiale. Vous pouvez également créer une adresse privée ou une adresse avec une adresse statique, mais qui est également abordée dans ce didacticiel.

Exécutez l’applet de commande suivante pour créer votre adresse IP publique.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Création de l’interface réseau

Nous sommes maintenant prêts à créer l’interface réseau qui utilise notre machine virtuelle. Nous utiliserons l’applet de commande [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) pour créer notre interface réseau dans le groupe de ressources créé précédemment et les nom et emplacement, sous-réseau et les adresse IP publique définie précédemment.

Exécutez l’applet de commande suivante pour créer votre interface réseau.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurer un objet de la machine virtuelle

Maintenant que nous disposons de ressources de stockage et de réseau définies, nous sommes prêts à définir des ressources de calcul pour l’ordinateur virtuel. Pour notre didacticiel, nous sera spécifier la taille de la machine virtuelle et les différentes propriétés du système d’exploitation et l’interface réseau que nous avons créée précédemment, définir le stockage blob, puis spécifiez le disque du système d’exploitation.

### <a name="create-the-vm-object"></a>Créer l’objet ordinateur virtuel

Nous allons commencer en spécifiant la taille de la machine virtuelle. Pour ce didacticiel, nous spécifions un DS13. L’applet de commande [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) nous permet de créer un objet configurable virtual machine avec le nom et la taille définie à l’aide des variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer l’objet ordinateur virtuel.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Créer un objet d’informations d’identification pour stocker le nom et le mot de passe pour les informations d’identification de l’administrateur local

Avant que nous pouvons définir les propriétés de système d’exploitation de la machine virtuelle, il faut fournir les informations d’identification pour le compte administrateur local sous la forme d’une chaîne sécurisée. Pour ce faire, nous allons utiliser l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Exécuter l’applet de commande suivante et, dans la fenêtre de demande d’informations d’identification de Windows PowerShell, tapez le nom et le mot de passe à utiliser pour le compte d’administrateur local sur la machine virtuelle Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Définir les propriétés de système d’exploitation de la machine virtuelle

Nous sommes maintenant prêts définir les propriétés de système d’exploitation de la machine virtuelle. Nous allons utiliser l’applet de commande [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) pour définir le type de système d’exploitation comme Windows, nécessitent l' [agent de l’ordinateur virtuel](virtual-machines-windows-classic-agents-and-extensions.md) pour être installé, spécifiez que l’applet de commande permet la mise à jour automatique et définir le nom de l’ordinateur virtuel, le nom de l’ordinateur et les informations d’identification en utilisant les variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour définir les propriétés du système d’exploitation de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Ajoutez l’interface réseau sur l’ordinateur virtuel

Ensuite, nous allons ajouter l’interface réseau que nous avons créée précédemment sur l’ordinateur virtuel. Nous allons utiliser l’applet de commande [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) pour ajouter l’interface réseau à l’aide de la variable d’interface réseau que vous avez définie précédemment.

Exécutez l’applet de commande suivante pour définir l’interface du réseau de votre machine virtuelle.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Définir l’emplacement de stockage des blob pour le disque devant être utilisé par la machine virtuelle

Ensuite, nous allons définir l’emplacement de stockage des blob pour le disque devant être utilisé par l’ordinateur virtuel en utilisant les variables que vous avez définie précédemment.

Exécutez l’applet de commande suivante pour définir l’emplacement de stockage du blob.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Définir des propriétés d’un disque de la machine virtuelle du système d’exploitation

Ensuite, nous allons définir le système d’exploitation propriétés d’un disque de l’ordinateur virtuel. Nous utiliserons l’applet de commande [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) pour spécifier que le système d’exploitation de l’ordinateur virtuel sera proviennent d’une image, pour définir la mise en cache en lecture seule (car SQL Server est installé sur le même disque) et définir le nom de la machine virtuelle et le disque du système d’exploitation définies à l’aide de variables que nous avons défini plus haut.

Exécutez l’applet de commande suivante pour définir les propriétés d’un disque de votre machine virtuelle du système d’exploitation.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Spécifier l’image de la plate-forme de l’ordinateur virtuel

Il est notre dernière étape de configuration pour spécifier l’image de la plate-forme pour notre machine virtuelle. Pour notre didacticiel, nous utilisons la dernière image de 2016 version CTP de SQL Server. Nous allons utiliser l’applet de commande [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) à utiliser cette image comme défini par les variables que vous avez définie précédemment.

Exécutez l’applet de commande suivante pour spécifier l’image de la plate-forme de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Créer la machine virtuelle de SQL

Maintenant que vous avez terminé les étapes de configuration, vous êtes prêt à créer l’ordinateur virtuel. Nous utiliserons l’applet de commande [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) pour créer l’ordinateur virtuel en utilisant les variables que nous avons définie.

Exécutez l’applet de commande suivante pour créer votre machine virtuelle.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

La machine virtuelle est créée. Notez qu’un compte de stockage standard est créé pour les diagnostics de démarrage car le compte de stockage spécifié pour le disque de la machine virtuelle est un compte de stockage premium.

Vous pouvez désormais afficher cette machine dans le portail Azure pour afficher [son adresse IP publique et son nom de domaine pleinement qualifié](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Exemple de script

Le script suivant contient le script PowerShell complet pour ce didacticiel. Il suppose que vous avez déjà le programme d’installation l’abonnement Azure à utiliser avec les commandes **Add-AzureRmAccount** et **Sélectionnez-AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé l’ordinateur virtuel, vous êtes prêt à vous connecter à l’ordinateur virtuel à l’aide de la connexion RDP et le programme d’installation. Pour plus d’informations, voir [se connecter à un ordinateur de virtuel de SQL Server sur Azure (responsable de ressources)](virtual-machines-windows-sql-connect.md).
