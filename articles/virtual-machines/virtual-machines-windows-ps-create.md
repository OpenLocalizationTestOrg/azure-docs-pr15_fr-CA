<properties
    pageTitle="Créer une VM Azure à l’aide de PowerShell | Microsoft Azure"
    description="Utilisez PowerShell d’Azure et d’Azure Resource Manager permet de créer facilement une nouvelle machine virtuelle exécutant Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Créer un ordinateur virtuel de Windows à l’aide du Gestionnaire de ressources et PowerShell

Cet article vous montre comment créer rapidement un ordinateur virtuel de Azure exécutant Windows Server et les ressources que nécessaires à l’aide du [Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) et de PowerShell. 

Toutes les étapes de cet article sont requis pour créer une machine virtuelle et elle dure environ 30 minutes à effectuer la procédure. Remplacez les exemples de valeurs de paramètre dans les commandes avec des noms qui sont pertinents pour votre environnement.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : Installer PowerShell Azure

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .
        
## <a name="step-2-create-a-resource-group"></a>Étape 2 : Créer un groupe de ressources

Toutes les ressources doivent être contenus dans un groupe de ressources, donc vous permet de créer d’abord.  

1. Obtenir la liste des emplacements disponibles dans laquelle les ressources peuvent être créés.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Définir l’emplacement des ressources. Cette commande définit l’emplacement **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Créer un groupe de ressources. Cette commande crée le groupe de ressources nommé **myResourceGroup** à l’emplacement que vous définissez.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Étape 3 : Créer un compte de stockage

Un [compte de stockage](../storage/storage-introduction.md) est nécessaire pour stocker le disque dur virtuel qui est utilisé par l’ordinateur virtuel que vous créez. Les noms de compte de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des chiffres et des lettres minuscules uniquement.

1. Testez le nom du compte de stockage pour l’unicité. Cette commande vérifie le nom **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Si cette commande renvoie la **valeur True**, le nom proposé est unique dans Azure. 
    
2. Maintenant, créez le compte de stockage.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Étape 4 : Créer un réseau virtuel

Tous les ordinateurs virtuels font partie d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créer un sous-réseau pour le réseau virtuel. Cette commande crée un sous-réseau nommé **mySubnet** avec un préfixe d’adresse 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. Créez à présent le réseau virtuel. Cette commande crée un réseau virtuel nommé **myVnet** , à l’aide d’un préfixe d’adresse de **10.0.0.0/16**et du sous-réseau que vous avez créé.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Étape 5 : Créer une interface de réseau et adresse IP publique

Pour permettre la communication avec l’ordinateur virtuel dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et une interface réseau.

1. Créer l’adresse IP publique. Cette commande crée une adresse IP publique nommée **myPublicIp** avec une méthode de répartition de la **dynamique**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Créer l’interface réseau. Cette commande crée une interface de réseau nommée **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Étape 6 : Créer un ordinateur virtuel

Maintenant que vous disposez de tous les éléments en place, il est temps de créer l’ordinateur virtuel.

1. Exécutez cette commande pour définir le nom du compte administrateur et le mot de passe pour l’ordinateur virtuel.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Le mot de passe doit être à 12-123 caractères et avoir au moins une minuscule, un caractère majuscule, un chiffre et un caractère spécial. 
        
2. Créez l’objet de configuration de l’ordinateur virtuel. Cette commande crée un objet de configuration nommé **myVmConfig** qui définit le nom de la machine virtuelle et de la taille de la machine virtuelle.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Pour une liste des tailles disponibles pour une machine virtuelle, reportez-vous à la section [tailles pour les machines virtuelles dans Azure](virtual-machines-windows-sizes.md) .
    
3. Configurer les paramètres de système d’exploitation de la machine virtuelle. Cette commande définit le nom de l’ordinateur, le type de système d’exploitation et les informations d’identification de compte de la machine virtuelle.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Définir l’image à utiliser pour mettre en service la machine virtuelle. Cette commande définit l’image à utiliser pour la machine virtuelle Windows Server. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Pour plus d’informations sur la sélection des images à utiliser, consultez [Naviguer et sélectionnez images de machine virtuelle Windows dans Azure avec PowerShell ou l’interface CLI](virtual-machines-windows-cli-ps-findimage.md).
        
5. Ajouter l’interface réseau que vous avez créé à la configuration.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Définir le nom et l’emplacement du disque dur VM. Le fichier de disque dur virtuel est stocké dans un conteneur. Cette commande crée le disque dans un conteneur nommé **vhds/WindowsVMosDisk.vhd** dans le compte de stockage que vous avez créé.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Ajouter les informations de disque du système d’exploitation à la configuration de la machine virtuelle. Remplacez la valeur de **$diskName** par un nom pour le disque du système d’exploitation. Créez la variable et ajouter les informations de disque dans la configuration.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Enfin, créez l’ordinateur virtuel.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Étapes suivantes

- S’il y a des problèmes avec le déploiement, une étape suivante serait d’examiner les [déploiements de groupe de ressources de résolution des problèmes avec Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
- Apprenez à gérer la machine virtuelle que vous avez créé en passant en revue les [machines virtuelles de gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-ps-manage.md).
- Tirer parti de l’utilisation d’un modèle pour créer un ordinateur virtuel en utilisant les informations dans [créer un ordinateur virtuel de Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md)
