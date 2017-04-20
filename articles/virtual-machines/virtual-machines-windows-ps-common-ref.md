<properties 
   pageTitle="Les commandes PowerShell courantes pour les machines virtuelles | Microsoft Azure"
   description="Commandes PowerShell courants pour vous aider à créer et gérer vos ordinateurs virtuels dans Azure sous Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Commandes PowerShell courantes pour créer et gérer des ordinateurs virtuels

Cet article décrit certaines des commandes PowerShell d’Azure que vous pouvez utiliser pour créer et gérer des ordinateurs virtuels dans votre abonnement Azure.  Pour obtenir une aide plus détaillée avec des options et des commutateurs de ligne de commande spécifiques, vous pouvez utiliser **Get-Help** *commande*.

Pour plus d’informations sur l’installation de la version la plus récente de PowerShell d’Azure, en sélectionnant votre abonnement et votre compte d’ouverture de session, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-a-vm"></a>Créer un ordinateur virtuel

Tâche | Commande
-------------- | -------------------------
Créer une configuration de machine virtuelle | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName « vm_name » - VMSize « vm_size »<BR></BR><BR></BR>La configuration de la machine virtuelle est utilisée pour définir ou mettre à jour les paramètres de la machine virtuelle. La configuration est initialisée avec le nom de la machine virtuelle et sa [taille](virtual-machines-windows-sizes.md).
Ajouter des paramètres de configuration | $vm = $vm [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) - machine virtuelle-Windows - ComputerName « Nom_Ordinateur »-$cred - ProvisionVMAgent des informations d’identification - EnableAutoUpdate<BR></BR><BR></BR>Les paramètres de système d’exploitation, y compris les [informations d’identification](https://technet.microsoft.com/library/hh849815.aspx) sont ajoutés à l’objet de configuration que vous avez créée précédemment à l’aide de New-AzureRmVMConfig.
Ajoutez une interface réseau | $vm = [AzureRmVMNetworkInterface-ajouter](https://msdn.microsoft.com/library/mt619351.aspx) - VM $vm-Id $carte ID<BR></BR><BR></BR>Un ordinateur virtuel doit avoir une [interface réseau](virtual-machines-windows-ps-create.md) pour communiquer sur un réseau virtuel. Vous pouvez également utiliser [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) pour récupérer un objet d’interface réseau existante.
Spécifier une image de la plate-forme | $vm = publisher_name « [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) - VM $vm - éditeur »-offrent des références SKU-« product_sku » « publisher_offer »-Version « latest »<BR></BR><BR></BR>[Informations sur les images](virtual-machines-windows-cli-ps-findimage.md) est ajouté à l’objet de configuration que vous avez créée précédemment à l’aide de New-AzureRmVMConfig. L’objet retourné à partir de cette commande est uniquement utilisée lorsque vous affectez le disque du système d’exploitation d’utiliser une image de la plate-forme.
Définissez le disque du système d’exploitation d’utiliser une image de la plate-forme | $vm = [AzureRmVMOSDisk-ensemble](https://msdn.microsoft.com/library/mt603746.aspx) - VM $vm-http://mystore1.blob.core.windows.net/vhds/disk_name.vhd « nom « nom_disque » - VhdUri » - CreateOption FromImage<BR></BR><BR></BR>Le nom du disque système d’exploitation et de son emplacement de [stockage](../storage/storage-powershell-guide-full.md) est ajouté à l’objet de configuration que vous avez créé précédemment.
Définissez le disque du système d’exploitation pour utiliser une image généralisée | $vm = AzureRmVMOSDisk-ensemble - VM $vm-https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk « nom « nom_disque » - SourceImageUri. {guid}, .vhd » - VhdUri « https://mystore1.blob.core.windows.net/vhds/disk_name.vhd » - CreateOption FromImage-Windows<BR></BR><BR></BR>Le nom de l’emplacement du disque dans le [stockage](../storage/storage-powershell-guide-full.md) , le disque du système d’exploitation et l’emplacement de l’image source est ajouté à l’objet de configuration.
Définissez le disque du système d’exploitation pour utiliser une image spécialisée | $vm = AzureRmVMOSDisk-ensemble - VM $vm-http://mystore1.blob.core.windows.net/vhds/ « nom « name_of_disk » - VhdUri » CreateOption - joindre - Windows
Créer un ordinateur virtuel | [Nouveau-AzureRmVM]() - ResourceGroupName « nom_groupe_ressource »-emplacement « location_name » - VM $vm<BR></BR><BR></BR>Toutes les ressources sont créées dans un [groupe de ressources](../powershell-azure-resource-manager.md). La machine virtuelle doit être créée dans même [emplacement](https://msdn.microsoft.com/library/azure/dn495177.aspx) que le groupe de ressources. Avant d’exécuter cette commande, exécutez New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, AzureRmVMSourceImage de l’ensemble, Add-AzureRmVMNetworkInterface et AzureRmVMOSDisk de l’ensemble.

## <a name="get-information-about-vms"></a>Obtenir des informations sur des machines virtuelles

Tâche | Commande
-------------- | -------------------------
Machines virtuelles de liste dans un abonnement| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Machines virtuelles de liste dans un groupe de ressources | Get-AzureRmVM - ResourceGroupName « nom_groupe_ressource »<BR></BR><BR></BR>Pour obtenir une liste des groupes de ressources dans votre abonnement, utilisez [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obtenir des informations sur une machine virtuelle | Get-AzureRmVM - ResourceGroupName « nom_groupe_ressource »-nom « vm_name »

## <a name="manage-vms"></a>Gérer des ordinateurs virtuels

Tâche | Commande
-------------- | -------------------------
Démarrer une machine virtuelle | [Démarrer-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « vm_name »
Arrêter une machine virtuelle | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « vm_name »
Redémarrer une machine virtuelle en cours d’exécution | [Redémarrage-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « vm_name »
Supprimer un ordinateur virtuel | [Supprimer-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName « nom_groupe_ressource »-nom « vm_name »
Généraliser une machine virtuelle | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-nom « vm_name »-généralisé<BR></BR><BR></BR>Exécutez cette commande avant d’exécuter AzureRmVMImage de l’enregistrer.
Capture d’une machine virtuelle | [Enregistrer-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName « nom_groupe_ressource » - VMName « vm_name » - DestinationContainerName « image_container » - VHDNamePrefix « image_name_prefix »-chemin d’accès « C:\filepath\filename.json »<BR></BR><BR></BR>Un ordinateur virtuel doit être [arrêté et généralisée](virtual-machines-windows-generalize-vhd.md) à utiliser pour créer une image. Avant d’exécuter cette commande, exécutez Set-AzureRmVm.
Mise à jour d’une machine virtuelle | [Mise à jour-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName « nom_groupe_ressource » machine virtuelle - $vm<BR></BR><BR></BR>Obtenir la configuration de machine virtuelle en cours à l’aide de Get-AzureRmVM et modifier les paramètres de configuration sur l’objet ordinateur virtuel, puis exécutez la commande suivante.
Ajouter un disque de données à un ordinateur virtuel | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - VM $vm-https://mystore1.blob.core.windows.net/vhds/disk_name.vhd « nom « nom_disque » - VhdUri » - LUN #-mise en cache de ReadWrite - DiskSizeinGB # CreateOption - vide<BR></BR><BR></BR>Pour obtenir l’objet ordinateur virtuel, utilisez Get-AzureRmVM. Permet de spécifier le nombre LUN et la taille du disque. Exécuter la mise à jour-AzureRmVM pour appliquer les modifications de configuration de la machine virtuelle. Le disque que vous ajoutez n’est pas initialisé. Pour plus d’informations sur l’initialisation des disques lorsqu’ils sont ajoutés, voir [Gérer les Machines d’Azure virtuelles à l’aide de gestionnaire de ressources et de PowerShell](virtual-machines-windows-ps-manage.md).
Supprimer un disque de données à partir d’un ordinateur virtuel | [Supprimer-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - VM $vm-nom « nom_disque »<BR></BR><BR></BR>Pour obtenir l’objet ordinateur virtuel, utilisez Get-AzureRmVM. Exécuter la mise à jour-AzureRmVM pour appliquer les modifications de configuration de la machine virtuelle.
Ajouter une extension à une machine virtuelle | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName « nom_groupe_ressource »-emplacement « azure_location » - VMName « vm_name »-nom « nom_extension »-éditeur « publisher_name »-Type « type_extension » - TypeHandlerVersion « #. # »-paramètres $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Exécutez cette commande avec les [informations de configuration](virtual-machines-windows-extensions-configuration-samples.md) approprié pour l’extension que vous souhaitez installer.
Supprimer une extension de la machine virtuelle | [Supprimer-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName « nom_groupe_ressource »-vm_name « nom « nom_extension » - VMName »

## <a name="next-steps"></a>Étapes suivantes

- Consultez les étapes de base pour la création d’une machine virtuelle dans [créer un ordinateur virtuel de Windows à l’aide du Gestionnaire de ressources et de PowerShell](virtual-machines-windows-ps-create.md).

