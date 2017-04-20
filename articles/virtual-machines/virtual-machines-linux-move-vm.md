<properties
    pageTitle="Déplacer un ordinateur virtuel de Linux | Microsoft Azure"
    description="Déplacer une VM Linux à un autre groupe de ressource ou abonnement Azure dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Déplacer une VM Linux à un autre groupe d’abonnement ou d’une ressource

Cet article vous explique comment déplacer une VM Linux entre les groupes de ressources ou des abonnements. Déplacement d’un ordinateur virtuel entre des abonnements peut s’avérer pratique si vous avez créé un ordinateur virtuel dans un abonnement personnel et que vous voulez maintenant déplacer vers l’abonnement de votre société.

> [AZURE.NOTE] Nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et des scripts pour utiliser le nouveaux ID de ressource. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>L’infrastructure du langage commun Azure permet de déplacer une machine virtuelle 

Pour déplacer avec succès un ordinateur virtuel, vous devez déplacer la machine virtuelle et toutes ses ressources de prise en charge. Pour répertorier toutes les ressources d’un groupe de ressources et leurs numéros d’identification, utilisez la commande **show du groupe azure** . Il vous permet de canaliser la sortie de cette commande dans un fichier afin que vous pouvez copier et coller les ID de commandes ultérieures.

    azure group show <resourceGroupName>

Pour déplacer un ordinateur virtuel et ses ressources à un autre groupe de ressources, utilisez la commande CLI **déplacer des Ressources azure** . L’exemple suivant montre comment déplacer un ordinateur virtuel et les ressources principales que nécessaires. Nous utiliser le paramètre **-i** et que vous passez dans une liste séparée par des virgules (sans espaces) des ID pour les ressources à déplacer.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Si vous souhaitez déplacer l’ordinateur virtuel et ses ressources à un autre abonnement, ajouter la **--destination-subscriptionId & #60 ; destinationSubscriptionID & #, 62 ;** paramètre permet de spécifier l’abonnement de destination.

Si vous travaillez à partir de l’invite de commande sur un ordinateur Windows, vous devez ajouter un **$** devant les noms de variables lorsque vous les déclarez. Ce n’est pas nécessaire sous Linux.

Vous êtes invité à confirmer que vous souhaitez déplacer la ressource spécifiée. Tapez **Y** pour confirmer que vous souhaitez déplacer les ressources.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer des différents types de ressources entre les abonnements et les groupes de ressources. Pour plus d’informations, voir [déplacer des ressources vers le nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).    