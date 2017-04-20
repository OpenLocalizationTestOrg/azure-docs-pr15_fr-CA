<properties
   pageTitle="Gérer vos ordinateurs virtuels à l’aide de PowerShell d’Azure | Microsoft Azure"
   description="Découvrez les commandes que vous pouvez utiliser pour automatiser des tâches dans la gestion de vos machines virtuelles."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gérer vos ordinateurs virtuels à l’aide de PowerShell d’Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


De nombreuses tâches que vous effectuez quotidiennement pour gérer vos ordinateurs virtuels peuvent être automatisées à l’aide des applets de commande PowerShell d’Azure. Cet article vous donne exemples de commandes pour des tâches plus simples et des liens vers les articles qui indiquent les commandes pour des tâches plus complexes.

>[AZURE.NOTE] Si vous n’avez pas installé et configuré Azure PowerShell encore, vous pouvez obtenir des instructions dans l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Comment faire pour utiliser les commandes de l’exemple
Vous aurez besoin de remplacer une partie du texte dans les commandes dont le texte est adapté à votre environnement. Le < et > symboles indiquent le texte à remplacer. Lorsque vous remplacez du texte, supprimez les symboles, mais laissez les guillemets en place.

## <a name="get-a-vm"></a>Obtenir une machine virtuelle
Il s’agit d’une tâche de base que vous utilisez souvent. L’utiliser pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur un ordinateur virtuel ou un résultat à stocker dans une variable.

Pour obtenir des informations sur l’ordinateur virtuel, exécutez cette commande, en remplaçant tous les éléments dans les devis, y compris les caractères < et > :

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pour stocker le résultat dans la variable $vm, exécutez la commande :

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Ouvrez une session sur un ordinateur virtuel basé sur Windows

Exécutez ces commandes :

>[AZURE.NOTE] Vous pouvez obtenir le nom de service cloud et de la machine virtuelle à partir de l’affichage de la commande **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Arrêter une machine virtuelle

Exécutez la commande suivante :

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Ce paramètre permet de conserver l’IP virtuelle (VIP) du service cloud, dans le cas où il est la dernier VM dans ce service de cloud. <br><br> Si vous utilisez le paramètre StayProvisioned, vous serez toujours facturé pour la machine virtuelle.

## <a name="start-a-vm"></a>Démarrer une machine virtuelle

Exécutez la commande suivante :

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Connectez un disque de données
Cette tâche nécessite quelques étapes. Tout d’abord, vous utilisez la *** Add-AzureDataDisk *** applet de commande pour ajouter le disque à l’objet $vm. Ensuite, vous utilisez applet de commande **Update-AzureVM** à jour la configuration de la machine virtuelle.

Vous devez également décider si vous souhaitez joindre un nouveau disque ou un qui contient des données. La commande crée le fichier .vhd pour un nouveau disque et l’attache.

Pour attacher un nouveau disque, exécutez la commande suivante :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Pour attacher un disque de données existant, exécutez la commande suivante :

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage blob, exécutez la commande suivante :

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Créer un ordinateur virtuel basé sur Windows

Pour créer une nouvelle machine virtuelle Windows dans Azure, suivez les instructions dans [l’Utilisation de PowerShell Azure permet de créer et de préconfigurer des machines virtuelles basées sur Windows](virtual-machines-windows-classic-create-powershell.md). Les étapes de cette rubrique vous guide dans la création d’un PowerShell Azure commande définie qui crée un ordinateur virtuel basé sur Windows, qui peut être préconfiguré :

- Avec l’appartenance à un domaine Active Directory.
- Avec des disques supplémentaires.
- En tant que membre d’un équilibrage de charge défini.
- Avec une adresse IP statique.
