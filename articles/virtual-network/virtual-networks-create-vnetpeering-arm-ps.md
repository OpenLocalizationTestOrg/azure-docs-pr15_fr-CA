<properties
   pageTitle="Créer l’homologation de VNet à l’aide des applets de commande Powershell | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel à l’aide du portail Azure dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Créer l’homologation de VNet à l’aide des applets de commande Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet d’homologation, à l’aide de PowerShell, veuillez suivre les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

> [AZURE.NOTE] Applet de commande PowerShell de gestion VNet homologation est livré avec [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Lire les objets de réseau virtuel :

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Pour établir l’homologation de VNet, vous devez créer deux liens, une pour chaque direction. L’étape suivante crée un lien d’homologation VNet de VNet1 à VNet2 tout d’abord :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Sortie indique :

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Cette étape crée un lien d’homologation VNet de VNet2 à VNet1 :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Sortie indique :

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Une fois le lien d’homologation VNet est créé, vous pouvez voir l’état des liens ci-dessous :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Sortie indique :

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Il existe quelques propriétés configurables pour l’homologation de VNet :

  	|Option|Description|Par défaut|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Si l’espace de VNet égal à être inclus dans le cadre de la balise Virtual_network d’adressage|Oui|
  	|AllowForwardedTraffic|Si le trafic ne proviennent ne pas d’une VNet de ressources est accepté ou rejeté|N°|
  	|AllowGatewayTransit|Permet à l’hôte à utiliser la passerelle VNet VNet|N°|
  	|UseRemoteGateways|Utiliser passerelle de VNet de votre collègue. L’homologue VNet doit avoir une configuration de passerelle et AllowGatewayTransit est sélectionnée. Vous ne pouvez pas utiliser cette option si vous disposez d’une configuration de passerelle|N°|

    Chaque lien de l’homologation de VNet a l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess sur True pour un lien d’homologation VNet VNet1 à VNet2 et affectez-lui la valeur False pour le lien d’homologation VNet dans l’autre direction.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Vous pouvez exécuter Get-AzureRmVirtualNetworkPeering à vérifier à la valeur de la propriété après la modification. À partir de la sortie, vous pouvez voir Qu'allowforwardedtraffic modifie True après l’exécution d’applets de commande ci-dessus.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Une fois que l’homologation établie dans ce scénario, vous devez être en mesure d’initier les connexions à partir de n’importe quelle machine virtuelle à toute machine virtuelle de deux VNets. Par défaut, AllowVirtualNetworkAccess a la valeur True et VNet peering met en service les ACL appropriées pour permettre la communication entre VNets. Vous pouvez toujours appliquer les règles de groupe (NSG) de sécurité de réseau pour bloquer la connectivité entre des sous-réseaux ou des ordinateurs virtuels pour contrôler le grain fin d’accès entre deux réseaux virtuels.  Pour plus d’informations sur la création de règles NSG, reportez-vous à cet [article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer VNet d’homologation sur les abonnements à l’aide de PowerShell, veuillez suivre les étapes ci-dessous :

1. Connectez-vous à Azure avec privilèges utilisateur-A du compte d’abonnement-A et exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Ce n’est pas une exigence, homologation peut être établie même si les utilisateurs déclenchent individuellement des demandes d’homologation pour leurs VNets respectifs tant que les demandes correspondant à. Ajout d’un utilisateur privilégié de l’autre VNet en tant qu’utilisateur dans le VNet local rend plus facile d’effectuer la configuration.

2. Connectez-vous à Azure avec compte de privilèges à l’utilisateur B b-abonnement et exécuter l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. Dans A à l’utilisateur de la session de connexion, exécutée l’applet de commande ci-dessous :

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Dans la session de connexion de l’utilisateur-B, exécutez l’applet de commande ci-dessous :

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Après l’établie d’homologation, un ordinateur virtuel de VNet3 doit être en mesure de communiquer avec un ordinateur virtuel de VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez exécuter les applets de commande PowerShell ci-dessous pour établir l’homologation VNet.  Vous devez définir la propriété AllowForwardedTraffic sur True et lier de VNET1 à HubVNet, ce qui autorise le trafic entrant à partir en dehors de l’espace d’adressage VNet homologation.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Après que homologation est établie, vous pouvez reportez-vous à cet [article](virtual-network-create-udr-arm-ps.md) et définir un itinéraire défini par l’utilisateur (UDR) pour rediriger le trafic de VNet1 grâce à une appliance virtuelle pour utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez le définir à l’adresse IP de l’appliance virtuelle de l’homologue VNet HubVNet. Voici un exemple :

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Pour créer un VNet peering entre un réseau virtuel classique et un réseau virtuel du Gestionnaire de ressources Azure dans PowerShell, suivez les étapes ci-dessous :

1. Objet de réseau virtuel pour **VNET1**, le réseau virtuel du Gestionnaire de ressources Azure comme suit :

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Pour établir des VNet homologation dans ce scénario, un seul lien est nécessaire, en particulier un lien à partir de **VNET1** vers **VNET2**. Cette étape nécessite de connaître l’ID de ressource. de votre VNet classique Le format de l’ID groupe ressource ressemble à :

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Veillez à remplacer le SubscriptionID, ResourceGroupName et VirtualNetworkName avec les noms appropriés.

    Ceci est possible par le texte suivant :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Une fois la VNet lien d’homologation est créé, vous pouvez voir l’état des liens comme illustré dans la sortie ci-dessous :

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Supprimer l’homologation de VNet

1.  Pour supprimer l’homologation de VNet, vous devez exécuter l’applet de commande suivante :

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Après la suppression d’un lien dans une homologation de VNET, l’état des liaisons homologue passera à déconnecté. Dans cet état, vous ne pouvez pas recréer le lien jusqu'à ce que l’état du lien homologue devient initié. Nous vous recommandons de que vous supprimez les deux liaisons avant de recréer l’homologation VNet.
