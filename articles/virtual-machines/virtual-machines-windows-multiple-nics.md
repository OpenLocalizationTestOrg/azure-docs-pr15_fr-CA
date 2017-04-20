<properties
   pageTitle="Créer un ordinateur virtuel de Windows avec plusieurs cartes réseau | Microsoft Azure"
   description="Apprenez à créer un ordinateur virtuel de Windows avec plusieurs cartes réseau liée à l’aide de modèles Azure PowerShell ou le Gestionnaire de ressources."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Création d’une machine virtuelle de Windows avec plusieurs cartes réseau
Vous pouvez créer une machine virtuelle (VM) dans Azure qui possède plusieurs interfaces de réseau virtuel (NIC) attachés. Un scénario courant se serait d’avoir de sous-réseaux différents d’une connectivité front-end et back-end, ou d’un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes réseau attaché. Pour plus d’informations, y compris comment créer plusieurs cartes réseau dans vos propres scripts PowerShell, en savoir plus sur le [déploiement des ordinateurs virtuels de plusieurs cartes](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Différentes [tailles de machine virtuelle](virtual-machines-windows-sizes.md) prise en charge d’un nombre variable de cartes réseau, si la taille votre machine virtuelle en conséquence.

>[AZURE.WARNING] Vous devez associer plusieurs cartes réseau lorsque vous créez un ordinateur virtuel, vous ne pouvez pas ajouter des cartes réseau à un ordinateur virtuel existant. Vous pouvez [créer un ordinateur virtuel basé sur les disques virtuels d’origine](virtual-machines-windows-vhd-copy.md) et créer plusieurs cartes réseau lorsque vous déployez la machine virtuelle.

## <a name="create-core-resources"></a>Créer des ressources principales
Assurez-vous que vous disposez de [dernière Azure PowerShell installé et configuré](../powershell-install-configure.md). Ouvrez une session votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myVM`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUs` emplacement :

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Créer un compte de stockage pour stocker vos ordinateurs virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Créer des sous-réseaux et des réseaux virtuels
Définir deux sous-réseaux du réseau virtuel : un pour le trafic frontal et une pour le trafic de back-end. L’exemple suivant définit deux sous-réseaux, nommés `mySubnetFrontEnd` et `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Créez un réseau virtuel et les sous-réseaux. L’exemple suivant crée un réseau virtuel nommé `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Créer plusieurs cartes réseau
Créer deux cartes d’interface réseau, attacher une carte réseau sur le sous-réseau frontal et une carte réseau sur le sous-réseau du back-end. L’exemple suivant crée deux cartes d’interface réseau, nommées `myNic1` et `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

En général vous créez également un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour aider à gérer et répartir le trafic entre vos ordinateurs virtuels. L’article [plus détaillé multi-carte d’interface réseau virtuelle](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) vous guide à travers la création d’un groupe de sécurité du réseau et l’affectation des cartes réseau.


## <a name="create-the-virtual-machine"></a>Créer l’ordinateur virtuel
Maintenant commencer à créer la configuration de votre machine virtuelle. La taille de chaque machine virtuelle a une limite pour le nombre total de cartes réseau que vous pouvez ajouter à une machine virtuelle. Apprenez-en plus sur [les tailles de mémoire virtuelle de Windows](virtual-machines-windows-sizes.md). 

Tout d’abord, définissez vos informations d’identification de la machine virtuelle la `$cred` variable comme suit :

```powershell
$cred = Get-Credential
```

L’exemple suivant définit un ordinateur virtuel nommé `myVM` et utilise une taille de mémoire virtuelle qui prend en charge de deux cartes d’interface réseau (`Standard_DS2_v2`) :

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Créer le reste de la configuration de votre machine virtuelle. L’exemple suivant crée un ordinateur virtuel de la R2 de Windows Server 2012 :

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Joignez deux cartes réseau que vous avez créé précédemment :

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurer le stockage et le disque virtuel pour votre nouvelle machine virtuelle :

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Enfin, créez un ordinateur virtuel :

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Création de plusieurs cartes réseau à l’aide des modèles du Gestionnaire de ressources
Azure modèles du Gestionnaire de ressources permet de définir votre environnement de fichiers JSON déclaratives. Vous pouvez lire une [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). Modèles du Gestionnaire de ressources permettent de créer plusieurs instances d’une ressource au cours du déploiement, comme la création de plusieurs cartes réseau. *Copie* vous permet de spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Plus d’informations sur la [Création de plusieurs instances à l’aide de la *copie*](../resource-group-create-multiple.md). 

Vous pouvez également utiliser un `copyIndex()` puis ajouter un nombre à un nom de ressource, ce qui vous permet de créer `myNic1`, `MyNic2`, etc.. Vous trouverez ci-dessous un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Vous trouverez un exemple complet de [Création de plusieurs cartes réseau à l’aide des modèles du Gestionnaire de ressources](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Assurez-vous de consulter [les tailles de mémoire virtuelle de Windows](virtual-machines-windows-sizes.md) lors de la création d’une machine virtuelle avec plusieurs cartes réseau. Soyez attentif au nombre maximal de cartes d’interface réseau prend en charge la taille de chaque machine virtuelle. 

Gardez à l’esprit que vous ne pouvez pas ajouter des cartes réseau à un ordinateur virtuel existant, vous devez créer toutes les cartes réseau lorsque vous déployez la machine virtuelle. Prenez soin lors de la planification de votre déploiement pour vous assurer que vous disposez tous la connectivité réseau requise dès le début.