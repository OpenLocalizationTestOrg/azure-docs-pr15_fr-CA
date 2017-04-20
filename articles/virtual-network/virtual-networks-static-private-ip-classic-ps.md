<properties 
   pageTitle="Comment définir un IP privée statique en mode classique, à l’aide de PowerShell | Microsoft Azure"
   description="Présentation statique privée IPs (DIP) et comment les gérer dans le mode classique et PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-powershell"></a>Comment définir une adresse IP privée (classic) dans PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [gérer une adresse IP statique privée, dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-static-private-ip-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous s’attendent à un environnement simple déjà créé. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test décrit dans [créer un VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Comment vérifier si une adresse IP spécifique n’est disponible
Pour vérifier si l' adresse IP *192.168.1.101* est disponible dans un VNet nommé *TestVNet*, exécutez la commande PowerShell suivante et vérifiez que la valeur de *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Sortie attendue :

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée lors de la création d’une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *TestService*, puis récupère une image à partir d’Azure, crée un ordinateur virtuel nommé *DNS01* dans le nouveau service cloud à l’aide de l’image récupérée, définit la machine virtuelle dans un sous-réseau nommé *FrontEnd*et définit *192.168.1.7* comme une adresse IP privée pour la machine virtuelle :

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Sortie attendue :

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment faire pour extraire des informations statiques d’adresse IP privées pour un ordinateur virtuel
Pour afficher les informations d’adresse IP privées statiques pour la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell suivante et observer les valeurs *d’adresse IP*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Sortie attendue :

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment faire pour supprimer une adresse IP privée statique à partir d’un ordinateur virtuel
Pour supprimer l’adresse IP privée statique ajouté à la machine virtuelle dans le script ci-dessus, exécutez la commande PowerShell suivante :
    
    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Sortie attendue :

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment faire pour ajouter une adresse IP privée pour un ordinateur virtuel existant
Pour ajouter un mappage statique privée adresse IP de la machine virtuelle créée à l’aide de ci-dessus, exécutez le script de commande suivante :

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Sortie attendue :

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP publiques (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API de reste IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).
