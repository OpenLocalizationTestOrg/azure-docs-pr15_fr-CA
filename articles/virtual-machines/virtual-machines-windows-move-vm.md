<properties
    pageTitle="Déplacer une machine virtuelle | Microsoft Azure"
    description="Déplacer un ordinateur virtuel de Windows vers un autre groupe de ressource ou abonnement Azure dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Déplacer un ordinateur virtuel de Windows vers un autre groupe d’abonnement ou d’une ressource Azure 

Cet article vous explique comment déplacer une machine virtuelle de Windows entre les groupes de ressources ou des abonnements. Déplacement entre des abonnements peut être pratique si vous avez créé un ordinateur virtuel dans un abonnement personnel et que vous voulez maintenant déplacer vers l’abonnement de votre société pour continuer votre travail.

> [AZURE.NOTE] Nouveaux ID de ressource seront créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et des scripts pour utiliser le nouveaux ID de ressource. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Utilisation de Powershell pour déplacer une machine virtuelle

Pour déplacer un ordinateur virtuel à un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour utiliser l’applet de commande Move-AzureRMResource, vous devez le nom de la ressource et le type de ressource. Vous pouvez obtenir à la fois à partir de l’applet de commande Rechercher-AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Pour déplacer un ordinateur virtuel, nous devons déplacer plusieurs ressources. Nous pouvons simplement créer des variables séparées pour chaque ressource et ensuite de les répertorier. Cet exemple inclut la plupart des ressources de base d’un ordinateur virtuel, mais vous pouvez ajouter d’autres selon vos besoins.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées. Tapez **Y** pour confirmer que vous souhaitez déplacer les ressources.

  
## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer des différents types de ressources entre les abonnements et les groupes de ressources. Pour plus d’informations, voir [déplacer des ressources vers le nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).    