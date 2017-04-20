<properties
   pageTitle="Ajout d’une passerelle de VNet à un réseau virtuel pour ExpressRoute à l’aide du Gestionnaire de ressources et PowerShell | Microsoft Azure"
   description="Cet article vous guide par l’ajout d’une passerelle de Vnet d’un VNet le Gestionnaire de ressources déjà créé pour ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
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
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide du Gestionnaire de ressources et PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Gestionnaire de ressources](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - classique](expressroute-howto-add-gateway-classic.md)


Cet article vous guidera tout au long de la procédure pour ajouter, redimensionner et supprimer une passerelle réseau virtuel (VNet) pour un VNet existant. Les étapes de cette configuration sont spécifiquement pour les VNets qui ont été créés à l’aide du **Gestionnaire de ressources du modèle de déploiement** , et qui sera être utilisée dans une configuration ExpressRoute. 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Avant de commencer

Vérifiez que vous avez installé les applets de commande PowerShell de Azure nécessaire pour cette configuration (1.0.2 ou version ultérieure). Si vous n’avez pas installé les applets de commande, vous devrez le faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation de PowerShell d’Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé la passerelle VNet, vous pouvez lier votre VNet d’un circuit de ExpressRoute. Voir [lien un réseau virtuel à un circuit de ExpressRoute](expressroute-howto-linkvnet-arm.md).
