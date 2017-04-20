<properties
   pageTitle="Déplacer les circuits ExpressRoute de classique pour le Gestionnaire de ressources | Microsoft Azure"
   description="Cette page décrit comment déplacer un circuit classique pour le modèle de déploiement du Gestionnaire de ressources."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Déplacer des circuits de ExpressRoute à partir de standard pour le modèle de déploiement du Gestionnaire de ressources

## <a name="configuration-prerequisites"></a>Configuration préalable

- Vous avez besoin de la dernière version des modules Azure PowerShell (au moins la version 1.0).
- Assurez-vous que vous avez examiné les [conditions préalables](expressroute-prerequisites.md), [routage des exigences](expressroute-routing.md)et [des flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Avant précédant davantage, passez en revue les informations fournies sous le [déplacement d’un circuit de ExpressRoute à partir de classique pour le Gestionnaire de ressources](expressroute-move.md). Vérifiez que vous avez parfaitement compris les limites et les limites de ce qui est possible.
- Si vous souhaitez déplacer un circuit Azure ExpressRoute à partir du modèle de déploiement classique pour le modèle de déploiement du Gestionnaire de ressources Azure, vous devez disposer du circuit entièrement configuré et opérationnel dans le modèle de déploiement classique.
- Assurez-vous que vous disposez d’un groupe de ressources qui a été créé dans le modèle de déploiement du Gestionnaire de ressources.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Déplacer le circuit de ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources

Vous devez déplacer un circuit de ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources afin que vous puissiez l’utiliser sur le standard et les modèles de déploiement du Gestionnaire de ressources. Pour cela, en exécutant les commandes PowerShell suivants.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Étape 1 : Collecter des détails de circuit à partir du modèle de déploiement classique

Vous devez rassembler des informations sur votre circuit ExpressRoute tout d’abord.

Se connecter à l’environnement classique Azure et rassembler la clé du service. Vous pouvez utiliser l’extrait suivant de PowerShell pour recueillir les informations :

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copiez la **clé de service** du circuit que vous souhaitez déplacer vers le modèle de déploiement du Gestionnaire de ressources.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Étape 2 : Se connecter à l’environnement du Gestionnaire de ressources et créer un nouveau groupe de ressources

Vous pouvez créer un nouveau groupe de ressources à l’aide de l’extrait de code suivant :

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Vous pouvez également utiliser un groupe de ressources existant si vous disposez déjà d’une.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Étape 3 : Déplacer le circuit de ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources

Vous êtes maintenant prêt à déplacer sur le circuit de ExpressRoute à partir de standard pour le modèle de déploiement du Gestionnaire de ressources. Passez en revue les informations fournies sous le [déplacement d’un circuit de ExpressRoute à partir de standard pour le modèle de déploiement du Gestionnaire de ressources](expressroute-move.md) avant de continuer.

Pour cela, en exécutant l’extrait suivant :

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Une fois le déplacement terminé, le nouveau nom est répertorié dans l’applet de commande précédent servira à l’adresse de la ressource. Le circuit est essentiellement renommé.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Activer un circuit de ExpressRoute pour les deux modèles de déploiement

Vous devez déplacer votre circuit ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources avant de contrôler l’accès au modèle de déploiement.

Exécutez l’applet de commande suivante pour activer l’accès pour les deux modèles de déploiement :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous serez en mesure d’afficher le circuit dans le modèle de déploiement classique.

Exécutez la commande suivante pour obtenir les détails du circuit ExpressRoute :

    get-azurededicatedcircuit

Vous devez être en mesure de voir la clé de service répertoriée. Vous pouvez désormais gérer les liens vers le circuit de ExpressRoute à l’aide de vos commandes de modèle standard de déploiement classique pour VNets classique et vos commandes ARM standard pour ARM VNETs. Les articles suivants vous guidera tout au long de comment gérer les liens vers le circuit de ExpressRoute :

- [Lier votre réseau virtuel à votre circuit ExpressRoute dans le modèle de déploiement du Gestionnaire de ressources](expressroute-howto-linkvnet-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Désactiver le circuit de ExpressRoute pour le modèle de déploiement classique

Exécutez l’applet de commande suivante pour désactiver l’accès au modèle de déploiement classique :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous ne serez pas en mesure d’afficher le circuit dans le modèle de déploiement classique.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, assurez-vous que vous effectuez les opérations suivantes :

- [Créer et modifier le routage pour circuit de votre ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
