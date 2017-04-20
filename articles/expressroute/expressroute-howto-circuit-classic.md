<properties
   pageTitle="Créer et modifier un circuit de ExpressRoute à l’aide du modèle de déploiement classique et PowerShell | Microsoft Azure"
   description="Cet article vous guide à travers les étapes de création et de mise en service d’un circuit de ExpressRoute. Cet article vous montre également comment créer des votre circuit et de vérifier le statut, la mise à jour ou le supprimer."
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
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Créer et modifier un circuit de ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Gestionnaire de ressources](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-circuit-arm.md)
[PowerShell - classique](expressroute-howto-circuit-classic.md)

Cet article vous guide tout au long de la procédure de création d’un circuit de ExpressRoute d’Azure à l’aide des applets de commande PowerShell et le modèle de déploiement classique. Cet article va également vous montrer comment créer un circuit de ExpressRoute et de vérifier le statut, la mise à jour ou le supprimer.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Avant de commencer

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. passer en revue les conditions requises et les articles de flux de travail

Assurez-vous que vous avez examiné les [conditions requises](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. Installez les dernières versions des modules PowerShell d’Azure 

Suivez les instructions pour obtenir des instructions étape par étape [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell d’Azure.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. Connectez-vous à votre compte Azure et sélectionnez un abonnement

1. Exécutez l’applet de commande suivante à l’aide d’une invite de commandes Windows PowerShell avec élévation de privilèges :

        Add-AzureAccount
2. Dans l’écran de connexion qui apparaît, connectez-vous à votre compte.

3. Obtenir la liste de vos abonnements.

        Get-AzureSubscription
4. Sélectionnez l’abonnement que vous souhaitez utiliser.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Créer et mettre en service un circuit de ExpressRoute

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importer les modules PowerShell pour ExpressRoute

 Si vous ne le n'avez pas déjà fait, vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour commencer à utiliser des applets de commande ExpressRoute. Vous importez les modules à l’emplacement où ils ont été installés sur votre ordinateur local. Selon la méthode que vous avez utilisé pour installer les modules, l’emplacement peut être différent de celui de l’exemple suivant. Modifier l’exemple si nécessaire.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtenir la liste des fournisseurs pris en charge, les emplacements et les bandes passantes

Avant de créer un circuit de ExpressRoute, vous devez la liste des fournisseurs de connectivité prises en charge, les emplacements et les options de bande passante.

L’applet de commande PowerShell `Get-AzureDedicatedCircuitServiceProvider` renvoie ces informations, que vous utiliserez dans les étapes suivantes :

    Get-AzureDedicatedCircuitServiceProvider

Vérifiez si votre fournisseur de connectivité y sont répertoriée. Notez les informations suivantes car vous en aurez besoin ultérieurement lorsque vous créez un circuit :

- Nom

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit de ExpressRoute.         

### <a name="3-create-an-expressroute-circuit"></a>3. création d’un circuit de ExpressRoute

L’exemple suivant montre comment créer une 200-Mbits/s à circuit de ExpressRoute à Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, remplacez ces informations lorsque vous effectuez votre demande.

>[AZURE.IMPORTANT] Votre circuit ExpressRoute sera facturé depuis le moment où qu'une clé de service est émise. Assurez-vous que vous effectuez cette opération lorsque le fournisseur de connectivité est prêt à mettre en service le circuit.


Voici un exemple de demande pour une nouvelle clé de service :

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Ou bien, si vous souhaitez créer un circuit de ExpressRoute avec le complément de la prime, utilisez l’exemple suivant. Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus d’informations sur le complément de la prime.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La réponse ne contient pas la clé du service. Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. liste de tous les circuits ExpressRoute

Vous pouvez exécuter la `Get-AzureDedicatedCircuit` commande pour obtenir la liste de tous les circuits de ExpressRoute que vous avez créé :


    Get-AzureDedicatedCircuit

L’effet est similaire à l’exemple suivant :

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez récupérer ces informations à tout moment à l’aide de la `Get-AzureDedicatedCircuit` applet de commande. L’appel sans paramètres affiche la liste de tous les circuits. Votre clé de service apparaît dans le champ de *clé de service* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. vous envoyer la clé du service à votre fournisseur de connectivité pour le provisionnement


*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de la mise en service sur le côté du prestataire de service. *État* indique l’état du côté Microsoft. Pour plus d’informations sur le circuit de mise en service des États, consultez l’article de [flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Lorsque vous créez un nouveau circuit de ExpressRoute, le circuit sera dans l’état suivant :

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Le circuit passera à l’état suivant lorsque le fournisseur de connectivité en l’activant pour vous :

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuit de ExpressRoute doit être dans l’état suivant pour pouvoir l’utiliser :

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. périodiquement vérifier le statut et l’état de la touche de circuit

Cela vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois que le circuit a été configuré, *ServiceProviderProvisioningState* s’affiche sous la forme *Provisioned* comme indiqué dans l’exemple suivant :

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. Créez votre configuration de routage

Reportez-vous à la [ExpressRoute de configuration de routage circuit (créer et modifier le circuit peerings)](expressroute-howto-routing-classic.md) article pour obtenir des instructions pas à pas.

>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement aux circuits qui sont créés avec des fournisseurs de services qui proposent des services de connectivité de 2 couche. Si vous utilisez un fournisseur de services qui offre géré de couche 3 services (généralement un IP VPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. lier un réseau virtuel à un circuit de ExpressRoute

Ensuite, liez un réseau virtuel à votre circuit ExpressRoute. Pour obtenir des instructions détaillées, reportez-vous aux [circuits de ExpressRoute de liaison à des réseaux virtuels](expressroute-howto-linkvnet-classic.md) . Si vous avez besoin créer un réseau virtuel à l’aide du modèle de déploiement classique pour ExpressRoute, voir [Création d’un réseau virtuel pour ExpressRoute](expressroute-howto-vnet-portal-classic.md) pour obtenir des instructions.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtention de l’état d’un circuit de ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de la `Get-AzureCircuit` applet de commande. L’appel sans paramètres affiche la liste de tous les circuits.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir des informations sur un circuit de ExpressRoute spécifique en passant la clé de service en tant que paramètre à l’appel :

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Vous pouvez obtenir des descriptions détaillées de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modification d’un circuit de ExpressRoute

Vous pouvez modifier certaines propriétés d’un circuit de ExpressRoute sans impact sur la connectivité.

Vous pouvez effectuer les opérations sans interruption de service :

- Activer ou désactiver un module complémentaire à la prime ExpressRoute pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la bande passante d’un circuit de mise à niveau n’est pas pris en charge.
- Modifier le plan de mesure à partir des données de mesuré de données illimité. Notez que modifier le plan de mesure à partir de données illimité est mesuré de données n’est pas pris en charge.
- Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus d’informations sur les limites et restrictions.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le complément de la prime ExpressRoute

Vous pouvez activer le complément de la prime ExpressRoute pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Votre circuit aura maintenant les fonctions ExpressRoute prime complémentaire sont activées. Notez que nous allons commencer facturation pour la fonctionnalité de module complémentaire premium dès que la commande a été exécutée.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le complément de la prime ExpressRoute

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources qui sont supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieure à 10 avant de mettre à niveau à partir de la prime à la norme. Si vous ne cela, votre demande de mise à jour échoue et vous serez facturé le taux de prime.

- Vous devez supprimer le lien de tous les réseaux virtuels dans d’autres régions géopolitiques. Si vous ne cela, votre demande de mise à jour échoue et vous serez facturé le taux de prime.

- Votre table de routage doit être inférieure à 4 000 itinéraires pour homologation privé. Si la taille de la table de routage est supérieure à 4 000 itinéraires, la session BGP supprime et n’être réactivée jusqu'à ce que le nombre de préfixes annoncés devient inférieur à 4 000.

Vous pouvez désactiver le module complémentaire premium de ExpressRoute pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Mise à jour de la bande passante de circuit ExpressRoute

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour les options de prise en charge de la bande passante de votre fournisseur. Vous pouvez choisir n’importe quelle taille est supérieure à la taille de votre circuit existant tant que le port physique (sur lequel est créé le circuit) permet.

>[AZURE.IMPORTANT] Vous ne pouvez pas réduire la bande passante d’un circuit de ExpressRoute sans interruption de service. Déclassement de la bande passante nécessite que vous deprovision le circuit ExpressRoute et puis remettez à disposition un nouveau circuit de ExpressRoute.

Après avoir déterminé la taille que vous avez besoin, vous pouvez utiliser la commande suivante pour redimensionner votre circuit :

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Votre circuit sera ont été de taille chez Microsoft. Vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations de leur côté pour faire correspondre cette modification. Notez que nous allons commencer facturation vous pour l’option de mise à jour de la bande passante à partir de ce point.

Si vous voyez le message d’erreur suivant lors de l’augmentation de la bande passante du circuit, elle signifie qu’il ne reste aucun suffisamment de bande passante sur le port physique où votre circuit existant est créé. Vous devez supprimer ce circuit et créer un nouveau circuit de la taille souhaitée. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation et suppression d’un circuit de ExpressRoute

Notez les points suivants :

- Vous devez supprimer le lien de tous les réseaux virtuels du circuit ExpressRoute pour que cette opération réussisse. Vérifiez si vous avez des réseaux virtuels qui sont liés au circuit si cette opération échoue.

- Si l’état de mise en service de fournisseur de service de circuit ExpressRoute est **Provisioning** ou **Provisioned** vous devez travailler avec votre fournisseur de services à deprovision le circuit de leur côté. Nous continuerons à réserver des ressources et vous les facturer jusqu'à ce que le fournisseur du service termine le circuit de mise hors service et nous avertit.

- Si le fournisseur de service a arrêtés le circuit (l’état de mise en service de fournisseur de service est défini à **non configuré**), vous pouvez ensuite supprimer le circuit. Ceci va arrêter la facturation pour le circuit.

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, assurez-vous que vous effectuez les opérations suivantes :

- [Créer et modifier le routage pour circuit de votre ExpressRoute](expressroute-howto-routing-classic.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)
