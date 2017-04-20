<properties 
   pageTitle="Instance de niveau IP publics (ILPIP) | Microsoft Azure"
   description="Présentation des ILPIP (PIP) et comment les gérer"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Présentation IP public au niveau instance
Une instance IP publics niveau (ILPIP) est une adresse IP publique que vous pouvez affecter directement à votre instance de machine virtuelle ou le rôle, plutôt que pour le service en nuage résidant dans votre instance de rôle ou de la machine virtuelle. Cela n’a pas lieu de l’adresse IP virtuelle (IP virtuel) est affecté à votre service cloud. Au lieu de cela, il s’agit d’une adresse IP supplémentaire que vous pouvez utiliser pour vous connecter directement à votre instance de rôle ou de la machine virtuelle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md). 

Assurez-vous de que comprendre le fonctionnement des [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

>[AZURE.NOTE] Dans le passé, un ILPIP a été appelé un PIP, qui est l’abréviation de l’adresse IP publique. 

![Différence entre ILPIP et VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Comme illustré à la Figure 1, le service en nuage est accessible à l’aide d’une adresse IP virtuelle, alors que les ordinateurs virtuels individuels normalement accessibles à l’aide de VIP :&lt;numéro de port&gt;. En assignant une ILPIP à une machine virtuelle spécifique, cette dernière est accessible directement à l’aide de cette adresse IP.

Lorsque vous créez un service en nuage dans Azure, les enregistrements A DNS correspondants sont créés automatiquement pour permettre l’accès au service via un nom de domaine pleinement qualifié (FQDN) au lieu d’utiliser l’adresse IP virtuelle réelle. Le même processus se produit pour le ILPIP, ce qui permet d’accéder à l’instance de rôle ou de la machine virtuelle par nom de domaine complet au lieu de la ILPIP. Par exemple, si vous créez un service en nuage nommé *contosoadservice*, et de configurer un rôle web nommé *contosoweb* avec deux instances, Azure inscrira le suivant A des enregistrements pour les instances :

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Vous pouvez affecter qu’un seul ILPIP pour chaque instance du rôle ou de la machine virtuelle. Vous pouvez utiliser jusqu'à 5 ILPIP par abonnement. À ce stade, ILPIP n’est pas pris en charge pour les machines virtuelles de plusieurs cartes.

## <a name="why-should-i-request-an-ilpip"></a>Pourquoi dois-je demander un ILPIP ?
Si vous souhaitez être en mesure de vous connecter à votre instance de rôle ou de la machine virtuelle en une adresse IP directement, au lieu d’utiliser le cloud service VIP :&lt;numéro de port&gt;, demander une ILPIP pour votre machine virtuelle ou d’instance de votre rôle.
- **Le mode FTP passif** - ayant une ILPIP sur votre ordinateur virtuel, vous pouvez recevoir du trafic sur n’importe quel port, vous devrez pas ouvrir d’un point de terminaison pour recevoir le trafic. Cela permet des scénarios tels que le mode FTP passif où les ports sont sélectionnés de façon dynamique.
- **IP sortants** - trafic sortant provenant de la machine virtuelle s’éteint avec le ILPIP comme source et il identifie de façon unique la machine virtuelle à des entités externes.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Comment demander une ILPIP lors de la création de la machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *FTPService*, puis récupère une image à partir d’Azure, crée un ordinateur virtuel nommé *FTPInstance* , à l’aide de l’image récupérée, définit la machine virtuelle pour utiliser un ILPIP et ajoute l’ordinateur virtuel pour le nouveau service :

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Comment faire pour récupérer des informations de ILPIP pour une machine virtuelle
Pour afficher les informations de ILPIP de la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell suivante et observer les valeurs de *PublicIPAddress* et de *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Comment faire pour supprimer un ILPIP à partir d’un ordinateur virtuel
Pour supprimer la ILPIP ajouté à la machine virtuelle dans le script ci-dessus, exécutez la commande PowerShell suivante :
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>L’ajout d’un ILPIP à un ordinateur virtuel existant
Pour ajouter une ILPIP de la machine virtuelle créée en utilisant le script ci-dessus, exécutez la commande suivante :

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>L’association d’une ILPIP à une machine virtuelle à l’aide d’un fichier de configuration de service
Vous pouvez également associer une ILPIP à une machine virtuelle en utilisant un fichier de configuration (CSCFG) du service. L’exemple xml ci-après montre comment configurer un service en nuage pour utiliser un ILPIP nommé *MyPublicIP* pour une instance de rôle : 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le fonctionnement de [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) dans le modèle de déploiement classique.

- Obtenir des informations sur les [adresses IP réservée](virtual-networks-reserved-public-ip.md).
 