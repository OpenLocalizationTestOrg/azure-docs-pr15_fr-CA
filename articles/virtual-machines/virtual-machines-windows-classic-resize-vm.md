<properties
    pageTitle="Redimensionner une VM Windows classique | Microsoft Azure"
    description="Redimensionner une machine virtuelle de Windows créée dans le modèle de déploiement classique, à l’aide de Powershell d’Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionner une VM Windows créées dans le modèle de déploiement classique

Cet article vous montre comment redimensionner une machine virtuelle Windows, créées dans le modèle de déploiement classique à l’aide de Powershell d’Azure.

Lorsque vous envisagez la possibilité de redimensionner une machine virtuelle, il existe deux concepts qui contrôlent la plage de tailles disponibles pour redimensionner l’ordinateur virtuel. Le premier concept est la région dans laquelle votre ordinateur virtuel est déployé. La liste de tailles de mémoire virtuelle disponibles dans la région est sous l’onglet Services de la page web de régions d’Azure. Le deuxième concept est le matériel qui héberge actuellement votre machine virtuelle. Les serveurs physiques qui héberge des ordinateurs virtuels sont regroupés dans des clusters de matériel commun. La méthode de modification d’une taille de mémoire virtuelle est différente selon si la nouvelle taille souhaitée pour la machine virtuelle est pris en charge par le cluster de matériel qui héberge actuellement la machine virtuelle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vous pouvez également [redimensionner une machine virtuelle créée dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Ajoutez votre compte

Vous devez configurer PowerShell Azure pour travailler avec des ressources Azure classiques. Suivez les étapes ci-dessous pour configurer PowerShell Azure pour gérer les ressources classiques.

1. À l’invite de PowerShell, tapez `Add-AzureAccount` et appuyez sur **entrée**. 
2. Saisissez l’adresse e-mail associée à votre abonnement Azure et cliquez sur **Continuer**. 
3. Tapez le mot de passe pour votre compte. 
4. Cliquez sur **connexion**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionner dans le même cluster de matériel

Pour redimensionner une machine virtuelle à une taille disponible dans le cluster de matériel hébergeant la machine virtuelle, procédez comme suit.

1. Exécutez la commande PowerShell suivante pour répertorier les tailles de mémoire virtuelle disponibles dans le cluster de matériel qui héberge le service de cloud qui contient l’ordinateur virtuel.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Exécutez les commandes suivantes pour redimensionner la machine virtuelle.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionner un nouveau cluster de matériel

Pour redimensionner une machine virtuelle à une taille qui n’est pas disponible dans le cluster de matériel hébergeant la machine virtuelle, le cloud service et tous les ordinateurs virtuels dans le service en nuage doivent être recréés. Chaque service en nuage est hébergé sur un cluster de matériel unique pour tous les ordinateurs virtuels dans le service en nuage doivent être d’une taille qui est pris en charge sur un cluster de matériel. La procédure suivante décrit comment redimensionner un ordinateur virtuel en supprimant puis en recréant le service en nuage.

1. Exécutez la commande PowerShell suivante pour répertorier les tailles de mémoire virtuelle disponibles dans la région. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Prenez note de tous les paramètres de configuration de chaque ordinateur virtuel dans le service en nuage qui contient de la machine virtuelle soit redimensionné. 
3. Supprimer tous les ordinateurs virtuels dans le service cloud l’option Conserver les disques pour chaque machine virtuelle.
4. Recréer l’ordinateur virtuel à être redimensionnée à l’aide de la taille souhaitée de la machine virtuelle.
5. Recréer tous les autres ordinateurs virtuels qui étaient dans le service cloud à l’aide d’une taille de mémoire virtuelle disponible dans le cluster de matériel maintenant hébergeant le service en nuage.

Un exemple de script pour la suppression et la recréation d’un service en nuage à l’aide d’une nouvelle taille de la mémoire virtuelle, vous pouvez trouver [ici](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Étapes suivantes

- [Redimensionner une machine virtuelle créée dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-resize-vm.md).