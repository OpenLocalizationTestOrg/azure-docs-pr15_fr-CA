<properties
   pageTitle="Vérifier une connexion passerelle | Microsoft Azure"
   description="Cet article vous montre comment vérifier une connexion de passerelle dans le modèle de déploiement du Gestionnaire de ressources"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Vérifier une connexion de passerelle

Vous pouvez vérifier votre connexion de passerelle de différentes manières. Cet article vous explique comment vérifier l’état d’une connexion de passerelle du Gestionnaire de ressources à l’aide du portail Azure et à l’aide de PowerShell.


## <a name="verify-using-powershell"></a>Vérifiez à l’aide de PowerShell

Vous devez installer la dernière version de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation des applets de commande Gestionnaire de ressources, consultez [L’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Étape 1 : Ouvrez une session votre compte Azure

1. Ouvrez la console PowerShell avec des privilèges élevés et vous connecter à votre compte.

        Login-AzureRmAccount

2. Vérifiez les abonnements pour le compte.

        Get-AzureRmSubscription 

3. Spécifier l’abonnement que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Étape 2 : Vérifier votre connexion


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Vérifier l’utilisation du portail Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez ajouter des ordinateurs virtuels à vos réseaux virtuels. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

