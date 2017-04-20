<properties 
   pageTitle="Comment faire pour déplacer une instance de la machine virtuelle ou un rôle à un sous-réseau différent"
   description="Apprenez à déplacer les ordinateurs virtuels et les instances de rôles à un sous-réseau différent"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Comment faire pour déplacer une instance de la machine virtuelle ou un rôle à un sous-réseau différent

Vous pouvez utiliser PowerShell pour déplacer vos ordinateurs virtuels à partir d’un sous-réseau à un autre dans le même réseau virtuel (VNet). Les instances de rôle peuvent être déplacés en modifiant le CSCFG, plutôt qu’à l’aide de PowerShell.

>[AZURE.NOTE] Cet article contient des informations se rapportant à des déploiements Azure classiques uniquement.

Pourquoi déplacer des ordinateurs virtuels vers un autre sous-réseau ? Migration de sous-réseau est utile lorsque le sous-réseau plus ancien est insuffisant et ne peut pas être développé en raison des machines virtuelles en cours d’exécution existantes de ce sous-réseau. Dans ce cas, vous pouvez créer un sous-réseau de nouveau, plus grand et migrer des ordinateurs virtuels vers le nouveau sous-réseau, puis une fois la migration terminée, vous pouvez supprimer l’ancien sous-réseau vide.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Comment faire pour déplacer un ordinateur virtuel vers un autre sous-réseau

Pour déplacer un ordinateur virtuel, exécutez l’applet de commande PowerShell de jeu-AzureSubnet, à l’aide de l’exemple ci-dessous en tant que modèle. Dans l’exemple ci-dessous, nous fournissons TestVM à partir de son sous-réseau présent, sous-réseau-2. Veillez à modifier l’exemple pour la fonction de votre environnement. Notez que chaque fois que vous exécutez l’applet de commande Update-AzureVM dans le cadre d’une procédure, il redémarrera votre ordinateur virtuel en tant que partie du processus de mise à jour.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Si vous avez spécifié une adresse IP privée interne statique pour votre machine virtuelle, vous devrez désactiver ce paramètre avant de déplacer la machine virtuelle sur un nouveau sous-réseau. Dans ce cas, utilisez le texte suivant :

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Pour déplacer une instance de rôle à un autre sous-réseau

Pour déplacer une instance de rôle, modifiez le fichier CSCFG. Dans l’exemple ci-dessous, nous passons « Role0 » dans le réseau virtuel *VNETName* à partir de son sous-réseau présent à *sous-réseau-2*. Étant donné que l’instance de rôle a déjà été déployé, vous allez simplement modifier le nom de sous-réseau = sous-réseau-2. Veillez à modifier l’exemple pour la fonction de votre environnement.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
