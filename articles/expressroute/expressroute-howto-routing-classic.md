<properties
   pageTitle="Comment faire pour configurer le routage pour un circuit de ExpressRoute pour le modèle de déploiement classique à l’aide de PowerShell | Microsoft Azure"
   description="Cet article vous guide à travers les étapes de création et de mise en service privé, public et Microsoft homologation d’un circuit de ExpressRoute. Cet article vous montre également comment vérifier l’état de la mise à jour ou de supprimer des peerings pour votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Créer et modifier le routage pour un circuit de ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Gestionnaire de ressources](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-routing-arm.md)
[PowerShell - classique](expressroute-howto-routing-classic.md)



Cet article vous guide tout au long de la procédure pour créer et gérer la configuration du routage pour un circuit de ExpressRoute à l’aide de PowerShell et le modèle de déploiement classique. Les étapes ci-dessous va également vous montrer comment créer des peerings pour un circuit de ExpressRoute et de vérifier le statut, la mise à jour ou le supprimer.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Configuration préalable

- Vous aurez besoin de la dernière version des modules PowerShell d’Azure. Vous pouvez télécharger le dernier module PowerShell à partir de la section PowerShell de la [page de téléchargements d’Azure](https://azure.microsoft.com/downloads/). Suivez les instructions dans la page [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) pour des instructions sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell d’Azure. 
- Assurez-vous que vous avez examiné la page [Configuration requise](expressroute-prerequisites.md) , la page [routage de configuration requise](expressroute-routing.md) et la page [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit de ExpressRoute actif. Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et ont le circuit activé par votre fournisseur de connectivité avant de continuer. Le circuit de ExpressRoute doit être dans un état de mise en service et activé pour vous permettre d’être en mesure d’exécuter les applets de commande décrites ci-dessous.

>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement aux circuits créés avec les prestataires de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services offrant des services gérés de couche 3 (en général un IPVPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

Vous pouvez configurer une, deux ou tous les trois peerings (public de privé, Azure Azure et Microsoft) pour un circuit de ExpressRoute. Vous pouvez configurer les peerings dans n’importe quel ordre que vous choisissez. Toutefois, il se peut que vous devez vous assurer de procéder à la configuration de chaque homologation à la fois. 

## <a name="azure-private-peering"></a>Homologation privé Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit de ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privé Azure

1. **Importez le module PowerShell de ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer des modules Azure et ExpressRoute dans la session PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit de ExpressRoute.**
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-classic.md) et de la mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous. 

3. **Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service.**

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Voir l’exemple ci-dessous.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit s’affiche sous la forme Provisioned et de l’activation. Si elle n’est pas le cas, travailler avec votre fournisseur pour obtenir votre circuit à l’état requis et l’état de connectivité.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurer une homologation privé Azure pour le circuit.**

    Assurez-vous d’avoir les éléments suivants avant de procéder aux étapes suivantes :

    - Un /30 sous-réseau pour le lien principal. Cela ne doit pas être partie de n’importe quel espace d’adressage réservé pour les réseaux virtuels.
    - Un /30 sous-réseau pour le lien secondaire. Cela ne doit pas être partie de n’importe quel espace d’adressage réservé pour les réseaux virtuels.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres. Vous pouvez utiliser une privée en tant que numéro de cette homologation. Assurez-vous que vous n’utilisez pas 65515.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer une homologation privé Azure pour votre circuit.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Veillez à spécifier votre numéro AS comme homologation ASN, pas le client ASN.

### <a name="to-view-azure-private-peering-details"></a>Pour afficher les détails de homologation privés Azure

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivant

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Pour mettre à jour la configuration de homologation privée Azure

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant. Dans l’exemple ci-dessous, l’ID du VLAN du circuit est mis à jour à partir de 100 à 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Pour supprimer une homologation privé Azure

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante.

>[AZURE.WARNING] Vous devez vous assurer que tous les réseaux virtuels sont liés dans le circuit de ExpressRoute avant d’exécuter cette applet de commande. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Homologation public Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit de ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation public Azure

1. **Importez le module PowerShell de ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer des modules Azure et ExpressRoute dans la session PowerShell. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit de ExpressRoute**
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-classic.md) et de la mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation public Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. **Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service**

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Voir l’exemple ci-dessous.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit s’affiche sous la forme Provisioned et de l’activation. Si elle n’est pas le cas, travailler avec votre fournisseur pour obtenir votre circuit à l’état requis et l’état de connectivité.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Configurer une homologation public Azure pour le circuit**

    Assurez-vous d’avoir les informations suivantes avant de poursuivre plus.

    - Un /30 sous-réseau pour le lien principal. Ce doit être un préfixe de IPv4 publique valide.
    - Un /30 sous-réseau pour le lien secondaire. Ce doit être un préfixe de IPv4 publique valide.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer une homologation public Azure pour votre circuit

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Veillez à spécifier votre numéro AS ASN homologation et pas le client ASN.

### <a name="to-view-azure-public-peering-details"></a>Pour afficher les détails de homologation publics Azure

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivant

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Pour mettre à jour la configuration de homologation publique Azure

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

L’ID VLAN du circuit est mis à jour à partir de 200 à 600 dans l’exemple ci-dessus.

### <a name="to-delete-azure-public-peering"></a>Pour supprimer une homologation public Azure

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Homologation de Microsoft

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation Microsoft pour un circuit de ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Pour créer l’homologation de Microsoft

1. **Importez le module PowerShell de ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer des modules Azure et ExpressRoute dans la session PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit de ExpressRoute**
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-classic.md) et de la mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. **Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service**

    Vous devez tout d’abord vérifier si le circuit de ExpressRoute est dans un état de Provisioned et de l’activation.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit s’affiche sous la forme Provisioned et de l’activation. Si elle n’est pas le cas, travailler avec votre fournisseur pour obtenir votre circuit à l’état requis et l’état de connectivité.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurer l’homologation pour le circuit de Microsoft**

    Assurez-vous que vous disposez des informations suivantes avant de continuer.

    - Un /30 sous-réseau pour le lien principal. Il doit s’agir d’un préfixe de IPv4 publique valide dont vous êtes propriétaire et inscrit dans un registre Internet régional / tri.
    - Un /30 sous-réseau pour le lien secondaire. Il doit s’agir d’un préfixe de IPv4 publique valide dont vous êtes propriétaire et inscrit dans un registre Internet régional / tri.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres.
    - Annoncé les préfixes : vous devez fournir une liste de tous les préfixes que vous envisagez de faire de la publicité sur la session BGP. Seuls les préfixes d’adresse IP publiques sont acceptés. Vous pouvez envoyer une liste séparée par des virgules si vous envisagez d’envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés pour vous dans un registre Internet régional / tri.
    - Client ASN : Dans le cas des préfixes de publicité qui ne sont pas enregistrés pour l’homologation comme nombre, vous pouvez spécifier le nombre de comme auquel ils sont inscrits. **Cette option est facultative**.
    - Nom de Registre de routage : Vous pouvez spécifier le RIR / tri par rapport à laquelle le nombre et les préfixes sont enregistrés.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative.**
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer les pering de Microsoft pour votre circuit

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>Pour afficher les détails d’homologation Microsoft

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivante.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Mise à jour de configuration d’homologation Microsoft

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Pour supprimer Microsoft homologation

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Étapes suivantes

Ensuite, il s’agit [d’un VNet d’un circuit de ExpressRoute de lien](expressroute-howto-linkvnet-classic.md).


-  Pour plus d’informations sur les workflows, reportez-vous à la section [ExpressRoute de flux de travail](expressroute-workflows.md).
-  Pour plus d’informations sur le circuit d’homologation, voir [circuits de ExpressRoute et de domaines de routage](expressroute-circuit-peerings.md).

