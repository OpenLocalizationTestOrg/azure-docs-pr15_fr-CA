<properties
   pageTitle="Avantages d’utilisation Azure hybride pour le serveur de la fenêtre | Microsoft Azure"
   description="Apprenez à optimiser les avantages de votre Windows Server Software Assurance pour retirer les licences sur site Azure"
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
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Avantages d’utilisation hybride Azure pour Windows Server

Pour les clients à l’aide de Windows Server avec Software Assurance, vous pouvez mettre des licences Windows Server vos locaux vers Azure et exécuter des machines virtuelles de Windows Server dans Azure à un coût réduit. L’avantage d’utiliser de hybride Azure vous permet d’exécuter des machines virtuelles de Windows Server dans Azure et payer uniquement pour le taux de calcul de base. Pour plus d’informations, consultez la [page Gestionnaire de licences des avantages d’utiliser Azure hybride](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Cet article explique comment déployer des machines virtuelles de serveur Windows dans Azure à utiliser cet avantage de licence.

> [AZURE.NOTE] Vous ne pouvez pas utiliser des images d’Azure Marketplace pour déployer les ordinateurs virtuels du serveur Windows utilisant l’avantage d’utiliser Azure hybride. Vous devez déployer vos ordinateurs virtuels à l’aide de modèles de PowerShell ou de gestionnaire de ressources à inscrire correctement vos ordinateurs virtuels comme éligible pour la remise des taux de calcul de base.

## <a name="pre-requisites"></a>Conditions préalables
Il existe un certain nombre de conditions requises pour utiliser Azure hybride utilisation avantage pour Windows serveur VMs dans Azure :

- Avoir installé le module PowerShell de Azure
- Votre disque dur virtuel de Windows Server ont téléchargées dans le stockage Azure

### <a name="install-azure-powershell"></a>Installer PowerShell Azure
Assurez-vous que vous avez [installé et configuré la dernière PowerShell Azure](../powershell-install-configure.md). Même si vous allez déployer vos ordinateurs virtuels à l’aide des modèles du Gestionnaire de ressources, vous devez toujours PowerShell Azure installé pour télécharger votre disque dur virtuel de Windows Server (voir l’étape suivante).

### <a name="upload-a-windows-server-vhd"></a>Télécharger un disque dur virtuel de Windows Server

Pour déployer un serveur Windows Azure ordinateur virtuel, vous devez d’abord créer un disque dur virtuel qui contient votre version de Windows Server de base. Ce disque dur virtuel doit être correctement préparé par Sysprep avant de le télécharger vers Azure. Vous pouvez [en savoir plus sur les exigences en matière de disque dur virtuel et les processus de Sysprep](./virtual-machines-windows-upload-image.md) et la [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sauvegarder la machine virtuelle avant d’exécuter Sysprep. Une fois que vous avez préparé votre disque dur virtuel, télécharger le disque dur virtuel à votre compte de stockage Azure à l’aide de la `Add-AzureRmVhd` applet de commande comme suit :

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server et Dynamics peuvent également utiliser le Gestionnaire de licences Software Assurance. Vous devez préparer l’image de Windows Server par l’installation des composants de votre application et fournir des clés de licence en conséquence, puis télécharger l’image de disque à Azure. Passez en revue la documentation appropriée pour l’exécution de Sysprep avec votre application, telles que les [Considérations sur l’installation d’un SQL Server à l’aide de Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [créer une Image de référence de 2016 SharePoint Server (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Vous pouvez également en savoir plus sur le [téléchargement du disque dur virtuel au processus d’Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Cet article met l’accent sur le déploiement des ordinateurs virtuels de Windows Server. Vous pouvez également déployer des machines virtuelles du Client Windows de la même manière. Dans les exemples suivants, vous remplacez `Server` avec `Client` correctement.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Déployer un ordinateur virtuel via PowerShell-Guide de démarrage rapide
Lors du déploiement de votre Windows Server VM via PowerShell, vous avez un paramètre supplémentaire de `-LicenseType`. Une fois que vous avez votre disque dur virtuel téléchargé vers Azure, vous créez une machine virtuelle en utilisant `New-AzureRmVM` et spécifier le type de licence comme suit :

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Vous pouvez [lire une procédure pas à pas plus détaillées sur le déploiement d’un ordinateur virtuel dans Azure via PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) ci-dessous ou consultez un guide plus descriptif sur les différentes étapes pour [créer un ordinateur virtuel de Windows à l’aide du Gestionnaire de ressources et de PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Déployer un ordinateur virtuel via le Gestionnaire de ressources
Dans votre gestionnaire de ressources des modèles, un paramètre supplémentaire pour `licenseType` peut être spécifié. Vous pouvez en savoir plus sur la [Création de modèles du Gestionnaire de ressources Azure](../resource-group-authoring-templates.md). Une fois que vous avez votre disque dur virtuel téléchargé vers Azure, vous modifier le modèle de gestionnaire de ressources pour inclure le type de licence dans le cadre du fournisseur compute et déployer votre modèle Normal :

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Vérifiez que votre machine virtuelle utilise l’avantage de licence
Une fois que vous avez déployé votre machine virtuelle par le biais de la méthode déploiement PowerShell ou le Gestionnaire de ressources, vérifiez le type de licence avec `Get-AzureRmVM` comme suit :
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

La sortie est semblable au suivant :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Cela contraste avec la VM suivante déployée sans l’avantage d’utiliser Azure hybride de la licence, par exemple un ordinateur virtuel déployé directement à partir de la galerie d’Azure :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Procédure pas à pas de PowerShell détaillées

Les étapes suivantes de PowerShell détaillées affichent un déploiement complet d’une machine virtuelle. Vous pouvez lire plus de contexte sur les applets de commande réel et différents composants en cours de création dans [créer un ordinateur virtuel de Windows à l’aide du Gestionnaire de ressources et de PowerShell](./virtual-machines-windows-ps-create.md). Passer par la création de votre groupe de ressources, le compte de stockage et le réseau virtuel, puis définir votre machine virtuelle et enfin créer votre machine virtuelle.
 
Tout d’abord, obtenir des informations d’identification en toute sécurité, définir un emplacement et un nom de groupe de ressources :

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Créer une adresse IP publique :

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Définissez votre carte réseau, du sous-réseau et du VNET :

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nom de votre machine virtuelle et créer une configuration de machine virtuelle :

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Définissez votre système d’exploitation :

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Ajoutez votre carte réseau de la machine virtuelle :

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Définir le compte de stockage à utiliser :

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Télécharger votre disque dur virtuel, convenablement préparé et attachez à votre machine virtuelle pour utiliser :

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Enfin, créez votre ordinateur virtuel et définir le type de licence pour exploiter l’avantage d’utiliser Azure hybride :

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en plus sur les [avantages d’utilisation Azure hybride de licence](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Pour en savoir plus sur [l’utilisation des modèles du Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md).
