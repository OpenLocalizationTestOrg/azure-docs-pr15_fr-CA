<properties
    pageTitle="Créer une copie de votre machine virtuelle de Windows | Microsoft Azure"
    description="Apprenez à créer une copie de votre spécialisé Azure VM fonctionnant sous Windows, dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Créer un ordinateur virtuel à partir d’un disque dur virtuel spécialisé

Créer une nouvelle machine virtuelle en attachant un disque dur virtuel spécialisé que le disque du système d’exploitation à l’aide de Powershell. Un disque dur virtuel spécialisé gère les comptes d’utilisateurs, d’applications et d’autres données sur l’état à partir de votre ordinateur virtuel d’origine. 

Si vous souhaitez créer un ordinateur virtuel à partir d’un disque dur virtuel généralisé, voir [créer un ordinateur virtuel à partir d’une image de disque dur virtuel généralisée](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Créer le sous-réseau et le vNet

Créer le vNet et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créer le sous-réseau. Cet exemple crée un sous-réseau nommé **mySubNet**, dans le groupe de ressources **myResourceGroup**et définit le préfixe d’adresse de sous-réseau **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Créer le vNet. Cet exemple définit le nom de réseau virtuel **myVnetName**, l’emplacement, à **l’Ouest des États-Unis**, et le préfixe d’adresse pour l’ordinateur virtuel à **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Créer une adresse IP publique et une carte d’interface réseau

Pour permettre la communication avec l’ordinateur virtuel dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et une interface réseau.

1. Créer l’adresse IP publique. Dans cet exemple, le nom d’adresse IP publique a **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Créer la carte réseau. Dans cet exemple, le nom de la carte réseau est défini à **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité du réseau et une règle de protocole RDP

Pour être en mesure de se connecter à votre ordinateur virtuel à l’aide de RDP, vous devez avoir une règle de sécurité qui autorise l’accès RDP sur le port 3389. Car le disque dur virtuel pour la nouvelle machine virtuelle a été créé à partir d’un fichier spécialisé VM, une fois que la machine virtuelle est créée, vous pouvez utiliser un compte existant sur l’ordinateur virtuel source qui a l’autorisation de se connecter à l’aide de RDP.

Cet exemple définit le nom NSG à **myNsg** et le nom de la règle RDP pour **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Pour plus d’informations sur les points de terminaison et les règles NSG, consultez [ouverture de ports à une machine virtuelle dans Azure à l’aide de PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Créer la configuration de la machine virtuelle

Définir la configuration de la machine virtuelle pour attacher le disque dur virtuel copié en tant que le disque dur virtuel du système d’exploitation.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Si vous avez des disques de données qui doivent être associés à la machine virtuelle, vous devez également ajouter le texte suivant : 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Les données et le système d’exploitation d’URL sur disque ressembler à ceci : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Vous le trouverez sur le portail en le conteneur de stockage cible de la navigation, cliquez sur le système d’exploitation ou les données de disque dur virtuel qui a été copié, puis copie le contenu de l’URL.


## <a name="create-the-vm"></a>Créer la machine virtuelle

Créer la machine virtuelle en utilisant les configurations que nous venons de créer.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si cette commande réussit, vous verrez la sortie comme suit :

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Vérifiez que l’ordinateur virtuel a été créé. 
 
Vous devez voir l’ordinateur virtuel nouvellement créé dans [Azure portal](https://portal.azure.com), sous **Parcourir** > **machines virtuelles**, ou à l’aide des commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre nouvel ordinateur virtuel, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **se connecter**et d’ouvrir le fichier RDP Remote Desktop. Les informations d’identification de compte de votre machine virtuelle d’origine permet de vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [comment vous connecter et ouvrir une session sur un ordinateur virtuel Azure exécutant Windows](virtual-machines-windows-connect-logon.md).







