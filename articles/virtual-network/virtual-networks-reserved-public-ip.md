<properties
   pageTitle="Réservé IP | Microsoft Azure"
   description="Comprendre les adresses IP réservée et comment les gérer"
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

# <a name="reserved-ip-overview"></a>Vue d’ensemble IP réservée
Les adresses IP dans Azure se répartissent en deux catégories : dynamique et réservé. Les adresses IP publiques gérés par Azure sont dynamiques par défaut. Cela signifie que l’adresse IP utilisée pour un service en nuage donné (VIP) ou pour accéder à une machine virtuelle ou rôle instance directement (ILPIP) pouvez modifier de temps en temps, lorsque les ressources sont arrêtés ou désalloué.

Pour empêcher la modification des adresses IP, vous pouvez réserver une adresse IP. IPs réservé peut servir uniquement une adresse VIP, s’assurer que l’adresse IP pour le service en nuage sera le même même lorsque des ressources sont arrêtés ou désalloué. En outre, vous pouvez convertir les IPs dynamique existante utilisée comme une adresse VIP à une adresse IP réservée.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment réserver une adresse IP publique statique en utilisant le [Gestionnaire de ressources du modèle de déploiement](virtual-network-ip-addresses-overview-arm.md).

Assurez-vous de que comprendre le fonctionnement des [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Lorsque j’ai besoin d’une adresse IP réservée ?
- **Vous souhaitez vous assurer que l’adresse IP réservé dans votre abonnement**. Si vous souhaitez réserver une adresse IP qui n’est pas libérée de votre abonnement en toutes circonstances, vous devez utiliser un IP publiques réservée.  
- **Vous souhaitez que votre adresse IP pour conserver votre service cloud, même dans un état arrêté ou libéré (VMs)**. Si vous souhaitez que votre service accessible à l’aide d’une adresse IP qui ne change pas, même lorsque les ordinateurs virtuels dans le service en nuage sont stop ou désalloué.
- **Vous voulez vous assurer que le trafic sortant à partir d’Azure utilise une adresse IP prévisible**. Vous devrez peut-être votre pare-feu local configuré pour autoriser uniquement le trafic provenant de certaines adresses IP. Par la réservation d’une adresse IP, vous connaissez l’adresse IP source et sera mise à jour de vos règles de pare-feu en raison d’une modification de l’IP.

## <a name="faq"></a>FAQ
1. Puis-je utiliser une adresse IP réservée pour tous les services Azure ?  
  - Adresses IP réservée utilisable uniquement pour les ordinateurs virtuels et les rôles d’instance de service cloud exposées via une adresse IP virtuelle.
1. Combien IPs réservés puis-je avoir ?  
  - À ce stade, tous les abonnements Azure sont autorisés à utiliser 20 IPs réservé. Toutefois, vous pouvez demander des IPs réservés supplémentaires. Reportez-vous à la page [d’abonnement et des limites de Service](../azure-subscription-service-limits.md) pour plus d’informations.
1. Existe-t-il des frais pour IPs réservé ?
  - Consultez les [Détails de tarification l’adresse IP réservée](http://go.microsoft.com/fwlink/?LinkID=398482) aux informations de tarification.
1. Comment pour réserver une adresse IP ?
  - Vous pouvez utiliser PowerShell ou l' [API REST de gestion Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) pour réserver une adresse IP dans une région donnée. Cette adresse IP réservée est associée à votre abonnement. Vous ne pouvez pas réserver une adresse IP à l’aide du portail de gestion.
1. Puis-je l’utiliser ceci avec affinité de groupe en fonction de VNets ?
  - Adresses IP réservée sont uniquement pris en charge dans VNets régional. Il n’est pas pris en charge pour les VNets qui sont associés à des groupes d’affinité. Pour plus d’informations sur l’association d’un VNet avec une région ou d’un groupe d’affinité, consultez [sur régional VNets et les affinités](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Comment gérer les VIP réservé

Avant de pouvoir utiliser des adresses IP réservée, vous devez l’ajouter à votre abonnement. Pour créer une adresse IP réservée du pool d’adresses d’IP publiques dans l’emplacement *Central des États-Unis* , exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Notez, cependant, que vous ne pouvez pas spécifier d’IP est réservée. Pour afficher les adresses IP sont réservés dans votre abonnement, exécutez la commande PowerShell suivante et notez les valeurs pour *ReservedIPName* et *adresse*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Une fois qu’une adresse IP est réservée, elle reste associé à votre abonnement jusqu'à ce que vous la supprimiez. Pour supprimer les adresses IP réservées, ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Comment faire pour réserver l’adresse IP d’un service cloud existant

Vous pouvez réserver l’adresse IP d’un service cloud existant en ajoutant le paramètre *- ServiceName* . Pour réserver l’adresse IP d’un service en nuage *TestService* dans l’emplacement *Central des États-Unis* , exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Comment associer un IP réservée à un nouveau service de cloud
Le script ci-dessous crée une nouvelle IP réservée, puis l’associe à un nouveau service en nuage nommé *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Lorsque vous créez un IP réservée à utiliser avec un service en nuage, vous devez toujours faire référence à la machine virtuelle à l’aide de *VIP :&lt;numéro de port >* pour la communication entrante. Réserver une adresse IP ne signifie pas que vous pouvez vous connecter directement à la machine virtuelle. Les adresses IP réservées est affecté au service de cloud qui l’ordinateur virtuel a été déployé sur. Si vous souhaitez vous connecter directement à un ordinateur virtuel par IP, vous devez configurer une adresse IP publique d’au niveau de l’instance. Une adresse IP publique d’au niveau de l’instance est un type d’adresse IP publique (appelé un ILPIP) qui est affectée directement à votre ordinateur virtuel. Il ne peut pas être réservé. Pour plus d’informations, reportez-vous à la section [Au niveau de l’Instance publique IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Comment faire pour supprimer une adresse IP réservée d’un déploiement en cours d’exécution
Pour supprimer les adresses IP réservées, ajouté au nouveau service créé dans le script ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Suppression d’une adresse IP réservée d’un déploiement en cours d’exécution ne supprime pas la réservation de votre abonnement. Il libère simplement l’adresse IP devant être utilisé par une autre ressource dans votre abonnement.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Comment associer un IP réservée à un déploiement en cours d’exécution
Le script ci-dessous crée un service cloud nommé *TestService2* avec un nouvel ordinateur virtuel nommé *TestVM2*et puis associe l’IP réservée existant nommé *MyReservedIP* pour le service en nuage.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Comment associer un IP réservée à un service cloud à l’aide d’un fichier de configuration de service
Vous pouvez également associer un IP réservée à un service cloud à l’aide d’un fichier de configuration (CSCFG) du service. L’exemple xml ci-après montre comment configurer un service en nuage pour utiliser une adresse IP virtuelle réservé nommé *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le fonctionnement de [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) dans le modèle de déploiement classique.

- Obtenir des informations sur [les adresses IP privées réservées](virtual-networks-reserved-private-ip.md).

- Obtenir des informations sur les [adresses de l’Instance de niveau IP publics (ILPIP)](virtual-networks-instance-level-public-ip.md).
