<properties 
   pageTitle="Lier un réseau virtuel à un circuit de ExpressRoute à l’aide de PowerShell | Microsoft Azure"
   description="Ce document fournit une vue d’ensemble de la façon de lier des réseaux virtuels (VNets) aux circuits de ExpressRoute à l’aide du Gestionnaire de ressources du modèle de déploiement et PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Lier un réseau virtuel à un circuit de ExpressRoute

> [AZURE.SELECTOR]
- [Azure Portal - Gestionnaire de ressources](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gestionnaire de ressources](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classique](expressroute-howto-linkvnet-classic.md)


Cet article vous aidera à lier des réseaux virtuels (VNets) pour circuits d’Azure ExpressRoute à l’aide du Gestionnaire de ressources du modèle de déploiement et PowerShell. Les réseaux virtuels peuvent être dans le même abonnement ou une partie d’un autre abonnement.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Configuration préalable

- Vous avez besoin de la dernière version des modules Azure PowerShell (au moins la version 1.0). Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .
- Vous devez passer en revue les [conditions requises](expressroute-prerequisites.md), [exigences de gamme](expressroute-routing.md)et [des flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit de ExpressRoute actif. 
    - Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et ont le circuit activé par votre fournisseur de connectivité. 
    - Vérifiez que vous disposez d’Azure peering privé configuré pour votre circuit. Voir l’article [configurer le routage](expressroute-howto-routing-arm.md) pour instructions de routage. 
    - Assurez-vous que peering privé Azure est configuré et l’homologation BGP entre votre réseau et de Microsoft est afin que vous pouvez activer la connectivité de bout en bout.
    - Assurez-vous que vous disposez d’un réseau virtuel et une passerelle réseau virtuel créé et entièrement mis en service. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mais veillez à utiliser `-GatewayType ExpressRoute`.

Vous pouvez lier jusqu'à 10 réseaux virtuels pour un circuit de ExpressRoute standard. Tous les réseaux virtuels doivent être dans la même région géopolitique lors de l’utilisation d’un circuit de ExpressRoute standard. 

Vous pouvez lier un réseaux virtuels en dehors de la région géopolitique du circuit ExpressRoute ou connecter un plus grand nombre de réseaux virtuels pour votre circuit ExpressRoute si vous avez activé le module complémentaire premium de ExpressRoute. Consultez le [Forum aux questions](expressroute-faqs.md) pour plus d’informations sur le complément de la prime.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans l’abonnement même à un circuit

Vous pouvez vous connecter à une passerelle de réseau virtuel pour un circuit de ExpressRoute à l’aide de l’applet de commande suivante. Vérifiez que la passerelle réseau virtuel est créée et qu’il est prête pour la liaison avant d’exécuter l’applet de commande :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans un autre abonnement à un circuit

Vous pouvez partager un circuit ExpressRoute sur plusieurs abonnements. La figure suivante illustre une simple principe de fonctionnement du partage pour des circuits de ExpressRoute au sein de plusieurs abonnements.

Chacun des plus petits nuages dans le nuage de grande taille est utilisé pour représenter les abonnements appartenant à différents départements au sein d’une organisation. Chacun des départements au sein de l’organisation peut utiliser leur propre abonnement pour le déploiement de leurs services, mais ils peuvent partager un même circuit ExpressRoute pour vous connecter à votre réseau local. Un seul département (dans cet exemple : IT) peut posséder le circuit de ExpressRoute. Autres abonnements au sein de l’organisation peuvent utiliser le circuit de ExpressRoute.

>[AZURE.NOTE] Les frais de connectivité et de bande passante pour le circuit dédié seront appliqueront au propriétaire du circuit de ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.

![Connectivité de cross-abonnement](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration

Le *propriétaire du circuit* est un utilisateur autorisé d’alimentation de la ressource de circuit ExpressRoute. Le propriétaire de circuit peut créer des autorisations qui peuvent être utilisées par *les utilisateurs du circuit*. *Les utilisateurs de circuit* sont des propriétaires des passerelles de réseau virtuel (qui ne sont pas dans le même abonnement comme le circuit ExpressRoute). *Les utilisateurs de circuit* peut échanger des autorisations (une autorisation par réseau virtuel).

Le *propriétaire de circuit* a la possibilité de modifier et de révoquer des autorisations à tout moment. Révocation d’un résultats d’autorisation de toutes les connexions de la liaison en cours de suppression de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations de propriétaire de circuit 

#### <a name="creating-an-authorization"></a>Création d’une autorisation
    
Le propriétaire du circuit crée une autorisation. Cela entraîne la création d’une clé d’autorisation qui peut être utilisée par un utilisateur de circuit pour connecter leurs passerelles réseau virtuel au circuit de ExpressRoute. Une autorisation est valide pour une seule connexion.

L’extrait de l’applet de commande suivant montre comment créer une autorisation :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

La réponse à cette contient la clé d’autorisation et l’état :

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Vérification des autorisations

Le propriétaire du circuit peut examiner toutes les autorisations qui sont délivrées sur un circuit particulier en exécutant l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Ajout d’autorisations

Le propriétaire de circuit peut ajouter des autorisations à l’aide de l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Suppression des autorisations

Le propriétaire du circuit peut révoquer/supprimer les autorisations à l’utilisateur en exécutant l’applet de commande suivante :

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur de circuit

L’utilisateur de circuit a besoin de l’ID de pair et d’une clé d’autorisation du propriétaire du circuit. La clé d’autorisation est un GUID.

ID de l’homologue est, peut être vérifiée à partir de la commande suivante.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Rachat des autorisations de connexion

L’utilisateur de circuit peut exécuter l’applet de commande suivant pour bénéficier d’une autorisation de lien :

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Libération des autorisations de connexion

Vous pouvez lancer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute au réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
