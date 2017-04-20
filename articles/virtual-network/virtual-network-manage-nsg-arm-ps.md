<properties 
   pageTitle="Gérer les NSGs dans le Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment gérer existant NSGs dans le Gestionnaire de ressources à l’aide de PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Gérer des NSGs à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Extraire des informations

Vous pouvez afficher votre NSGs existantes, récupérer les règles pour une NSG existant et savoir quelles ressources un NSG est associé à.

### <a name="view-existing-nsgs"></a>Afficher NSGs existants
Pour afficher toutes les NSGs existants dans un abonnement, exécutez le `Get-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

    Get-AzureRmNetworkSecurityGroup

Résultat attendu :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Pour afficher la liste des NSGs dans un groupe de ressources spécifique, exécutez le `Get-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Sortie attendue :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Liste de toutes les règles pour une NSG

Pour afficher les règles d’un NSG nommés **FrontEnd-NSG**, exécutez le `Get-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Sortie attendue :
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] Vous pouvez également utiliser `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` pour répertorier les règles par défaut du **NSG-FrontEnd** NSG.

### <a name="view-nsgs-associations"></a>Afficher les associations de NSGs

Pour afficher les ressources du NSG **NSG-frontal** est associer, exécuter la `Get-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Recherchez les propriétés **NetworkInterfaces** et de **sous-réseaux** , comme indiqué ci-dessous :

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

Dans l’exemple ci-dessus, le NSG n’est pas associé à toutes les interfaces réseau (cartes réseau), et il est associé à un sous-réseau nommé **FrontEnd**.

## <a name="manage-rules"></a>Gérer les règles

Vous pouvez ajouter des règles à un NSG existant, modifier les règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** à partir de n’importe quel ordinateur à **NSG-FrontEnd** NSG, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Add-AzureRmNetworkSecurityRuleConfig` applet de commande, comme indiqué ci-dessous.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Pour enregistrer les modifications apportées à la NSG, exécutez le `Set-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Afficher uniquement les règles de sécurité de sortie attendue :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Modifier une règle

Pour modifier la règle créée ci-dessus pour autoriser le trafic entrant à partir d' **Internet** uniquement, procédez comme suit.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Set-AzureRmNetworkSecurityRuleConfig` applet de commande, comme indiqué ci-dessous.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Pour enregistrer les modifications apportées à la NSG, exécutez le `Set-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Afficher uniquement les règles de sécurité de sortie attendue :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Supprimer une règle

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Remove-AzureRmNetworkSecurityRuleConfig` applet de commande, comme indiqué ci-dessous.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Pour enregistrer les modifications apportées à la NSG, exécutez le `Set-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Sortie attendue affichant uniquement les règles de sécurité, notez que la **règle de protocole https** n’est plus répertoriée :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gérer les associations

Vous pouvez associer un NSG à des sous-réseaux et des cartes réseau. Vous pouvez également dissocier un NSG à partir de n’importe quelle ressource qu'il est associé.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un NSG à une carte réseau

Pour associer les **NSG-FrontEnd** NSG à la **TestNICWeb1** de carte réseau, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Get-AzureRmNetworkInterface` applet de commande pour récupérer la carte d’interface réseau et stockez-le dans une variable, comme indiqué ci-dessous.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. La valeur de la propriété **NetworkSecurityGroup** de la variable de la **carte d’interface réseau** à la valeur de la variable **NSG** , comme indiqué ci-dessous.

        $nic.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez le `Set-AzureRmNetworkInterface` applet de commande comme indiqué ci-dessous.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Sortie attendue n'indiquant que la propriété **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un NSG à partir d’une carte réseau

Pour dissocier **NSG-FrontEnd** NSG à partir de la **TestNICWeb1** de carte réseau, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Get-AzureRmNetworkInterface` applet de commande pour récupérer la carte d’interface réseau et stockez-le dans une variable, comme indiqué ci-dessous.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. La valeur de la propriété **NetworkSecurityGroup** de la variable de la **carte d’interface réseau** sur **$null**, comme illustré ci-dessous.

        $nic.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez le `Set-AzureRmNetworkInterface` applet de commande comme indiqué ci-dessous.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Sortie attendue n'indiquant que la propriété **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un NSG à partir d’un sous-réseau

Pour dissocier **NSG-FrontEnd** NSG à partir du sous-réseau **frontal** , suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmVirtualNetwork` applet de commande pour extraire le VNet existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Exécuter le `Get-AzureRmVirtualNetworkSubnetConfig` applet de commande pour récupérer le sous-réseau **frontal** et stockez-le dans une variable, comme indiqué ci-dessous.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. La valeur de la propriété **NetworkSecurityGroup** de la variable de **sous-réseau** sur **$null**, comme illustré ci-dessous.

        $subnet.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez les `Set-AzureRmVirtualNetwork` applet de commande comme indiqué ci-dessous.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Afficher uniquement les propriétés du sous-réseau **frontal** de production prévue. Notez qu’il n’est pas une propriété pour **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Associer un NSG à un sous-réseau

Pour associer à nouveau les **NSG-FrontEnd** NSG au sous-réseau **FronEnd** , suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmVirtualNetwork` applet de commande pour extraire le VNet existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Exécuter le `Get-AzureRmVirtualNetworkSubnetConfig` applet de commande pour récupérer le sous-réseau **frontal** et stockez-le dans une variable, comme indiqué ci-dessous.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme indiqué ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. La valeur de la propriété **NetworkSecurityGroup** de la variable de **sous-réseau** sur **$null**, comme illustré ci-dessous.

        $subnet.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez les `Set-AzureRmVirtualNetwork` applet de commande comme indiqué ci-dessous.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Afficher uniquement la propriété **NetworkSecurityGroup** du sous-réseau **frontal** de sortie attendue :

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Supprimer un NSG

Vous ne pouvez supprimer un NSG que si elle n’a pas été associé à une ressource. Pour supprimer un NSG, suivez les étapes ci-dessous.

1. Pour vérifier les ressources associées à un NSG, exécutez le `azure network nsg show` comme indiqué dans les [associations d’affichage NSGs](#View-NSGs-associations).
2. Si le NSG est associée à des cartes réseau, exécutez le `azure network nic set` comme indiqué dans [Dissociate un NSG à partir d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si le NSG est associée à un sous-réseau, exécutez les `azure network vnet subnet set` comme indiqué dans [Dissociate un NSG à partir d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) pour chaque sous-réseau.
4. Pour supprimer du NSG, exécutez le `Remove-AzureRmNetworkSecurityGroup` applet de commande comme indiqué ci-dessous.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] Le **-Force** paramètre garantit que vous n’avez pas besoin de confirmer la suppression.

## <a name="next-steps"></a>Étapes suivantes

- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.