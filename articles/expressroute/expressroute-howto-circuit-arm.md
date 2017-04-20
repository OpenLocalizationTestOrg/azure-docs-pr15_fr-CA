<properties
   pageTitle="Créer et modifier un circuit de ExpressRoute à l’aide de gestionnaire de ressources et PowerShell | Microsoft Azure"
   description="Cet article décrit comment créer, configurer, vérifier, mettre à jour, supprimer et deprovision d’un circuit de ExpressRoute."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Créer et modifier un circuit de ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Gestionnaire de ressources](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-circuit-arm.md)
[PowerShell - classique](expressroute-howto-circuit-classic.md)


Cet article décrit comment créer un circuit Azure ExpressRoute à l’aide des applets de commande Windows PowerShell et le modèle de déploiement du Gestionnaire de ressources Azure. Cet article va également vous montrer comment vérifier l’état du circuit, mettre à jour, ou supprimer et il deprovision.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Avant de commencer


- Obtenir la dernière version des modules Azure PowerShell (au moins la version 1.0). Pour obtenir des instructions sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell, suivez les instructions de la [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

- Passez en revue les [conditions requises](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

## <a name="create-and-provision-an-expressroute-circuit"></a>Créer et mettre en service un circuit de ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Connectez-vous à votre compte Azure et sélectionnez votre abonnement

Pour commencer votre configuration, connectez-vous à votre compte Azure. Pour plus d’informations sur PowerShell, reportez-vous [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md). Utilisez les exemples suivants pour vous aider à vous connecter :

    Login-AzureRmAccount

Vérifiez les abonnements pour le compte :

    Get-AzureRmSubscription

Sélectionnez l’abonnement que vous souhaitez créer un circuit ExpressRoute pour :

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtenir la liste des fournisseurs pris en charge, les emplacements et les bandes passantes

Avant de créer un circuit de ExpressRoute, vous devez la liste des fournisseurs de connectivité prises en charge, les emplacements et les options de bande passante.

L’applet de commande PowerShell `Get-AzureRmExpressRouteServiceProvider` renvoie ces informations, que vous utiliserez dans les étapes suivantes :

    Get-AzureRmExpressRouteServiceProvider

Vérifiez si votre fournisseur de connectivité y sont répertoriée. Notez les informations suivantes car vous en aurez besoin ultérieurement lorsque vous créez un circuit :

- Nom

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit de ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. création d’un circuit de ExpressRoute

Si vous ne disposez pas d’un groupe de ressources, vous devez en créer un avant de créer votre circuit ExpressRoute. Vous pouvez le faire en exécutant la commande suivante :


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


L’exemple suivant montre comment créer une 200-Mbits/s à circuit de ExpressRoute à Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, remplacez ces informations lorsque vous effectuez votre demande. Voici un exemple de demande pour une nouvelle clé de service :

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Assurez-vous que vous spécifiez la couche de référence SKU correct et famille de références SKU :

- Niveau de stock détermine si une norme d’ExpressRoute ou d’un composant complémentaire ExpressRoute est activé. Vous pouvez spécifier *Standard* pour obtenir le point de stock ou la *prime* standard pour le complément de la prime.

- Famille de références SKU détermine le type de facturation. Vous pouvez spécifier *Metereddata* pour un plan de données contrôlé et *Unlimiteddata* pour un plan de données illimité. Notez que vous pouvez modifier le type de facturation à partir de *Metereddata* à *Unlimiteddata*, mais vous ne pouvez pas modifier le type de *Unlimiteddata* à *Metereddata*.


>[AZURE.IMPORTANT] Votre circuit ExpressRoute sera facturé depuis le moment où qu'une clé de service est émise. Assurez-vous que vous effectuez cette opération lorsque le fournisseur de connectivité est prêt à mettre en service le circuit.

La réponse contient la clé de service. Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. liste de tous les circuits de ExpressRoute

Pour obtenir une liste de tous les circuits de ExpressRoute que vous avez créé, exécutez le `Get-AzureRmExpressRouteCircuit` commande :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

La réponse sera semblable à l’exemple suivant :


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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Vous pouvez récupérer ces informations à tout moment à l’aide de la `Get-AzureRmExpressRouteCircuit` applet de commande. L’appel sans paramètres affiche la liste de tous les circuits. Votre clé de service apparaît dans le champ de *clé de service* :


    Get-AzureRmExpressRouteCircuit


La réponse sera semblable à l’exemple suivant :


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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. vous envoyer la clé du service à votre fournisseur de connectivité pour le provisionnement

*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de la mise en service sur le côté du prestataire de service. État indique l’état du côté Microsoft. Pour plus d’informations sur le circuit de mise en service des États, consultez l’article de [flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Lorsque vous créez un nouveau circuit de ExpressRoute, le circuit sera dans l’état suivant :


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Le circuit passera à l’état suivant lorsque le fournisseur de connectivité en l’activant pour vous :

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Pour vous permettre d’utiliser un circuit de ExpressRoute, il doit être dans l’état suivant :

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. périodiquement vérifier le statut et l’état de la touche de circuit

Vérification de l’état et l’état de la touche de circuit vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit a été configuré, *ServiceProviderProvisioningState* apparaît comme *Provisioned*, comme illustré dans l’exemple suivant :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse sera semblable à l’exemple suivant :


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

### <a name="7-create-your-routing-configuration"></a>7. Créez votre configuration de routage

Pour obtenir des instructions détaillées, consultez l’article de la [configuration de routage de circuit ExpressRoute](expressroute-howto-routing-arm.md) pour créer et modifier des circuits peerings.


>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement aux circuits qui sont créés avec des fournisseurs de services qui proposent des services de connectivité de 2 couche. Si vous utilisez un fournisseur de services qui offre géré de couche 3 services (généralement un IP VPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. lier un réseau virtuel à un circuit de ExpressRoute

Ensuite, liez un réseau virtuel à votre circuit ExpressRoute. Utilisez la [liaison de réseaux virtuels aux circuits de ExpressRoute](expressroute-howto-linkvnet-arm.md) lorsque vous travaillez avec le modèle de déploiement du Gestionnaire de ressources.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtention de l’état d’un circuit de ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de la `Get-AzureRmExpressRouteCircuit` applet de commande. L’appel sans paramètres affiche la liste de tous les circuits.

    Get-AzureRmExpressRouteCircuit


La réponse sera similaire à l’exemple suivant :


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


Vous pouvez obtenir des informations sur un circuit de ExpressRoute spécifique en passant le nom de groupe de ressources et circuit en tant que paramètre à l’appel :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse sera semblable à l’exemple suivant :


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


Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modification d’un circuit de ExpressRoute

Vous pouvez modifier certaines propriétés d’un circuit de ExpressRoute sans impact sur la connectivité.

Vous pouvez effectuer les opérations sans interruption de service :

- Activer ou désactiver un module complémentaire à la prime ExpressRoute pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la bande passante d’un circuit de mise à niveau n’est pas pris en charge.
- Modifier le plan de mesure à partir des données de mesuré de données illimité. Notez que modifier le plan de mesure à partir de données illimité est mesuré de données n’est pas pris en charge.
-  Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les restrictions, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le complément de la prime ExpressRoute

Vous pouvez activer le complément de la prime ExpressRoute pour votre circuit existant à l’aide de l’extrait de code PowerShell suivante :

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Le circuit aura maintenant les fonctions ExpressRoute prime complémentaire sont activées. Notez que nous allons commencer facturation pour la fonctionnalité de module complémentaire premium dès que la commande a été exécutée.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le complément de la prime ExpressRoute

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources qui sont supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Avant de mettre à niveau à partir de la prime standard, vous devez vous assurer que le nombre de réseaux virtuels qui sont liés au circuit est inférieur à 10. Si vous ne procédez pas ainsi, votre demande de mise à jour échoue et vous êtes facturé au taux de prime.

- Vous devez supprimer le lien de tous les réseaux virtuels dans d’autres régions géopolitiques. Si vous ne procédez pas ainsi, votre demande de mise à jour échoue et vous êtes facturé au taux de prime.

- Votre table de routage doit être inférieure à 4 000 itinéraires pour homologation privé. Si la taille de la table de routage est supérieure à 4 000 itinéraires, la session BGP supprime et n’être réactivée jusqu'à ce que le nombre de préfixes annoncés devient inférieur à 4 000.

Vous pouvez désactiver le module complémentaire à la prime ExpressRoute pour le circuit existant à l’aide de l’applet de commande PowerShell suivante :


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Mise à jour de la bande passante de circuit ExpressRoute

Pour les options de prise en charge de la bande passante de votre fournisseur, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n’importe quelle taille supérieure à la taille de votre circuit existant.

>[AZURE.IMPORTANT] Vous ne pouvez pas réduire la bande passante d’un circuit de ExpressRoute sans interruption de service. Déclassement de la bande passante exige que vous deprovision le circuit ExpressRoute et puis remettez à disposition un nouveau circuit de ExpressRoute.

Après avoir déterminé la taille que vous avez besoin, utilisez la commande suivante pour redimensionner votre circuit :


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


A la taille votre circuit chez Microsoft. Ensuite, vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations de leur côté pour faire correspondre cette modification. Après avoir effectué cette notification, nous commencerons vous facturer pour l’option de mise à jour de la bande passante.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Pour déplacer la référence SKU de mesurer dans un nombre illimité

Vous pouvez modifier la référence d’un circuit de ExpressRoute à l’aide de l’extrait de code PowerShell suivante :

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Pour contrôler l’accès à la classique et environnements de gestionnaire de ressources  

Passez en revue les instructions de [circuits ExpressRoute de déplacer à partir de standard pour le modèle de déploiement du Gestionnaire de ressources](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation et suppression d’un circuit de ExpressRoute

Notez les points suivants :

- Vous devez supprimer le lien de tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si tous les réseaux virtuels sont liés au circuit.

- Si l’état de mise en service de fournisseur de service de circuit ExpressRoute est **Provisioning** ou **Provisioned** vous devez travailler avec votre fournisseur de services à deprovision le circuit de leur côté. Nous continuerons à réserver des ressources et vous les facturer jusqu'à ce que le fournisseur du service termine le circuit de mise hors service et nous avertit.

- Si le fournisseur de service a arrêtés le circuit (l’état de mise en service de fournisseur de service est défini à **non configuré**), vous pouvez ensuite supprimer le circuit. Ceci va arrêter la facturation pour le circuit

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, assurez-vous que vous effectuez les opérations suivantes :

- [Créer et modifier le routage pour circuit de votre ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
