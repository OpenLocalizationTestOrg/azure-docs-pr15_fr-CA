<properties
   pageTitle="Modifier des préfixes d’adresses IP de passerelle réseau local et l’IP de la passerelle | Microsoft Azure"
   description="Cet article vous guide dans la modification des préfixes d’adresse IP de la passerelle du réseau local"
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
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modifier les paramètres de la passerelle réseau local à l’aide de PowerShell

Parfois possible de modifier les paramètres de la passerelle du réseau local AddressPrefix ou GatewayIPAddress. Les instructions ci-dessous vous permettra de modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
    
Vous devez installer la dernière version de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="to-modify-ip-address-prefixes"></a>Pour modifier des préfixes d’adresse IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Pour modifier l’adresse IP de la passerelle

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion passerelle. Reportez-vous à la section [vérifier une connexion passerelle](vpn-gateway-verify-connection-resource-manager.md).

