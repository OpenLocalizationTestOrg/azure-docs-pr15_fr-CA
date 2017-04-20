<properties 
   pageTitle="Contrôler le routage et utilisez les appliances virtuelles à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à contrôler le routage dans VNets utilisation de PowerShell dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Contrôle le routage et l’utilisation des boîtiers virtuels (classique) à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

L’exemple PowerShell Azure commandes ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, créez l’environnement [créer un VNet (classique) à l’aide de PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Créer le UDR pour le sous-réseau de front-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de front-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`New-AzureRouteTable`** applet de commande pour créer une table de routage pour le sous-réseau de front-end.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Sortie :

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Exécuter le **`Set-AzureRoute`** applet de commande pour créer un itinéraire dans la table de routage créé ci-dessus pour envoyer tout le trafic destiné au sous-réseau back-end (192.168.2.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Sortie :

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Exécuter le **`Set-AzureSubnetRouteTable`** applet de commande pour associer la table de routage créé précédemment avec le sous-réseau **frontal** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer le UDR pour le sous-réseau back-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de back-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`New-AzureRouteTable`** applet de commande pour créer une table de routage pour le sous-réseau back-end.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Exécuter le **`Set-AzureRoute`** applet de commande pour créer un itinéraire dans la table de routage créé ci-dessus pour envoyer tout le trafic destiné au sous-réseau front-end (192.168.1.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Exécuter le **`Set-AzureSubnetRouteTable`** applet de commande pour associer la table de routage créé précédemment avec le sous-réseau **back-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Activer le routage IP sur l’ordinateur virtuel FW1
Pour activer le transfert de l’ordinateur virtuel FW1 IP, suivez les étapes ci-dessous.

1. Exécuter le **`Get-AzureIPForwarding`** applet de commande pour vérifier le statut de transfert IP

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Sortie :

        Disabled

2. Exécuter le **`Set-AzureIPForwarding`** commande pour activer le transfert IP pour l’ordinateur virtuel *FW1* .

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
