<properties
   pageTitle="Comment faire pour configurer le routage pour un circuit de ExpressRoute | Microsoft Azure"
   description="Cet article vous guide à travers les étapes de création et de mise en service privé, public et Microsoft homologation d’un circuit de ExpressRoute. Cet article vous montre également comment vérifier l’état de la mise à jour ou de supprimer des peerings pour votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Créer et modifier le routage pour un circuit de ExpressRoute


> [AZURE.SELECTOR]
[Azure Portal - Gestionnaire de ressources](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-routing-arm.md)
[PowerShell - classique](expressroute-howto-routing-classic.md)



Cet article vous guide à travers les étapes pour créer et gérer la configuration du routage pour un circuit de ExpressRoute à l’aide de PowerShell et le modèle de déploiement du Gestionnaire de ressources Azure.  Les étapes ci-dessous va également vous montrer comment créer des peerings pour un circuit de ExpressRoute et de vérifier le statut, la mise à jour ou le supprimer. 


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Configuration préalable

- Vous aurez besoin de la dernière version des modules PowerShell d’Azure, version 1.0 ou ultérieure. 
- Assurez-vous que vous avez examiné la page [Configuration requise](expressroute-prerequisites.md) , la page [routage de configuration requise](expressroute-routing.md) et la page [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit de ExpressRoute actif. Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et ont le circuit activé par votre fournisseur de connectivité avant de continuer. Le circuit de ExpressRoute doit être dans un état de mise en service et activé pour vous permettre d’être en mesure d’exécuter les applets de commande décrites ci-dessous.

Ces instructions s’appliquent uniquement aux circuits créés avec les prestataires de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services offrant des services gérés de couche 3 (en général un IPVPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

>[AZURE.IMPORTANT] Actuellement, nous n’effectuent pas de peerings configurés par les fournisseurs de service via le portail de gestion de service. Nous travaillons sur l’activation de cette fonctionnalité bientôt. Veuillez vérifier avec votre fournisseur de services avant de configurer le protocole BGP peerings.

Vous pouvez configurer une, deux ou tous les trois peerings (public de privé, Azure Azure et Microsoft) pour un circuit de ExpressRoute. Vous pouvez configurer les peerings dans n’importe quel ordre que vous choisissez. Toutefois, il se peut que vous devez vous assurer de procéder à la configuration de chaque homologation à la fois. 

## <a name="azure-private-peering"></a>Homologation privé Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit de ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privé Azure

1. Importez le module PowerShell de ExpressRoute.
    
    Vous devez installer la dernière version installer PowerShell à partir de la [Galerie de PowerShell](http://www.powershellgallery.com/) et importer les modules du Gestionnaire de ressources Azure dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Vous devez exécuter PowerShell en tant qu’administrateur.

        Install-Module AzureRM

        Install-AzureRM

    Importer tous les modules de AzureRM.* dans la plage de version sémantique connue

        Import-AzureRM

    Vous pouvez également importer simplement d’un module de sélection dans la plage de version sémantique connue 
        
        Import-Module AzureRM.Network 

    Ouvrez une session sur votre compte

        Login-AzureRmAccount

    Sélectionnez l’abonnement que vous souhaitez créer le circuit de ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Créer un circuit de ExpressRoute.
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-arm.md) et de la mise en service par le fournisseur de connectivité. 

    Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous. 

3. Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service.

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Voir l’exemple ci-dessous.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    L’effet est similaire à l’exemple ci-dessous :

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Configurer une homologation privé Azure pour le circuit.

    Assurez-vous d’avoir les éléments suivants avant de procéder aux étapes suivantes :

    - Un /30 sous-réseau pour le lien principal. Cela ne doit pas être partie de n’importe quel espace d’adressage réservé pour les réseaux virtuels.
    - Un /30 sous-réseau pour le lien secondaire. Cela ne doit pas être partie de n’importe quel espace d’adressage réservé pour les réseaux virtuels.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres. Vous pouvez utiliser une privée en tant que numéro de cette homologation. Assurez-vous que vous n’utilisez pas 65515.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer une homologation privé Azure pour votre circuit.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Veillez à spécifier votre numéro AS comme homologation ASN, pas le client ASN.

### <a name="to-view-azure-private-peering-details"></a>Pour afficher les détails de homologation privés Azure

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivant

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Pour mettre à jour la configuration de homologation privée Azure

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant. Dans l’exemple ci-dessous, l’ID du VLAN du circuit est mis à jour à partir de 100 à 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Pour supprimer une homologation privé Azure

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante.

>[AZURE.WARNING] Vous devez vous assurer que tous les réseaux virtuels sont liés dans le circuit de ExpressRoute avant d’exécuter cette applet de commande. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Homologation public Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit de ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation public Azure

1. Importez le module PowerShell de ExpressRoute.
    
    Vous devez installer la dernière version installer PowerShell à partir de la [Galerie de PowerShell](http://www.powershellgallery.com/) et importer les modules du Gestionnaire de ressources Azure dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Vous devez exécuter PowerShell en tant qu’administrateur.

        Install-Module AzureRM

        Install-AzureRM

    Importer tous les modules de AzureRM.* dans la plage de version sémantique connue

        Import-AzureRM

    Vous pouvez également importer simplement d’un module de sélection dans la plage de version sémantique connue 
        
        Import-Module AzureRM.Network 

    Ouvrez une session sur votre compte

        Login-AzureRmAccount

    Sélectionnez l’abonnement que vous souhaitez créer le circuit de ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Créer un circuit de ExpressRoute.
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-arm.md) et de la mise en service par le fournisseur de connectivité. 

    Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation public Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service.

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Voir l’exemple ci-dessous.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    L’effet est similaire à l’exemple ci-dessous :

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Configurer une homologation public Azure pour le circuit.

    Assurez-vous d’avoir les informations suivantes avant de poursuivre plus.

    - Un /30 sous-réseau pour le lien principal. Ce doit être un préfixe de IPv4 publique valide.
    - Un /30 sous-réseau pour le lien secondaire. Ce doit être un préfixe de IPv4 publique valide.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer une homologation public Azure pour votre circuit

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Veillez à spécifier votre numéro AS ASN homologation et pas le client ASN.

### <a name="to-view-azure-public-peering-details"></a>Pour afficher les détails de homologation publics Azure

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivant

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Pour mettre à jour la configuration de homologation publique Azure

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

L’ID VLAN du circuit est mis à jour à partir de 200 à 600 dans l’exemple ci-dessus.

### <a name="to-delete-azure-public-peering"></a>Pour supprimer une homologation public Azure

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Homologation de Microsoft

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d’homologation Microsoft pour un circuit de ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Pour créer l’homologation de Microsoft

1. Importez le module PowerShell de ExpressRoute.
    
    Vous devez installer la dernière version installer PowerShell à partir de la [Galerie de PowerShell](http://www.powershellgallery.com/) et importer les modules du Gestionnaire de ressources Azure dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Vous devez exécuter PowerShell en tant qu’administrateur.

        Install-Module AzureRM

        Install-AzureRM

    Importer tous les modules de AzureRM.* dans la plage de version sémantique connue

        Import-AzureRM

    Vous pouvez également importer simplement d’un module de sélection dans la plage de version sémantique connue 
        
        Import-Module AzureRM.Network 

    Ouvrez une session sur votre compte

        Login-AzureRmAccount

    Sélectionnez l’abonnement que vous souhaitez créer le circuit de ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Créer un circuit de ExpressRoute.
    
    Suivez les instructions pour créer un [circuit de ExpressRoute](expressroute-howto-circuit-arm.md) et de la mise en service par le fournisseur de connectivité. 

    Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez demander votre fournisseur de connectivité pour activer une homologation privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service.

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Voir l’exemple ci-dessous.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    L’effet est similaire à l’exemple ci-dessous :

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Configurer l’homologation pour le circuit de Microsoft.

    Assurez-vous que vous disposez des informations suivantes avant de continuer.

    - Un /30 sous-réseau pour le lien principal. Il doit s’agir d’un préfixe de IPv4 publique valide dont vous êtes propriétaire et inscrit dans un registre Internet régional / tri.
    - Un /30 sous-réseau pour le lien secondaire. Il doit s’agir d’un préfixe de IPv4 publique valide dont vous êtes propriétaire et inscrit dans un registre Internet régional / tri.
    - Un ID de réseau VLAN valide pour établir cette homologation sur. S’assurer qu’aucune homologation du circuit n’utilise le même ID de VLAN.
    - En tant que numéro d’homologation. Vous pouvez utiliser 2 octets et de 4 octets sous forme de nombres.
    - Annoncé les préfixes : vous devez fournir une liste de tous les préfixes que vous envisagez de faire de la publicité sur la session BGP. Seuls les préfixes d’adresse IP publiques sont acceptés. Vous pouvez envoyer une liste séparée par des virgules si vous envisagez d’envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés pour vous dans un registre Internet régional / tri.
    - Client ASN : Dans le cas des préfixes de publicité qui ne sont pas enregistrés pour l’homologation comme nombre, vous pouvez spécifier le nombre de comme auquel ils sont inscrits. **Cette option est facultative**.
    - Nom de Registre de routage : Vous pouvez spécifier le RIR / tri par rapport à laquelle le nombre et les préfixes sont enregistrés.
    - Un hachage MD5, si vous choisissez d’en utiliser un. **Cette option est facultative.**
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer l’homologation pour votre circuit de Microsoft

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>Pour obtenir des détails d’homologation Microsoft

Vous pouvez obtenir les détails de configuration à l’aide de l’applet de commande suivante.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Mise à jour de configuration d’homologation Microsoft

Vous pouvez mettre à jour une partie de la configuration à l’aide de l’applet de commande suivant.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Pour supprimer Microsoft homologation

Vous pouvez supprimer votre configuration homologation en exécutant la cmdlet suivante.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Étapes suivantes

Étape suivante, [un VNet d’un circuit de ExpressRoute de lien](expressroute-howto-linkvnet-arm.md).

-  Pour plus d’informations à propos des workflows de ExpressRoute afficher le [flux de travail de ExpressRoute](expressroute-workflows.md).

-  Pour plus d’informations sur le circuit d’homologation, voir [circuits de ExpressRoute et de domaines de routage](expressroute-circuit-peerings.md).

-  Pour plus d’informations sur l’utilisation de réseaux virtuels, consultez [vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md).

