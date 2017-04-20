<properties
   pageTitle="Comment créer des NSGs dans le Gestionnaire de ressources Azure à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment créer et déployer des NSGs dans le Gestionnaire de ressources Azure à l’aide de PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-in-resource-manager-by-using-powershell"></a>Comment créer des NSGs dans le Gestionnaire de ressources à l’aide de PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer des NSGs dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

L’exemple PowerShell commandes ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, tout d’abord créer l’environnement de test par le déploiement de [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)et cliquez sur **déployer vers Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, suivez les instructions dans le portail.

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Comment créer le NSG pour le sous-réseau de front-end
Pour créer un NSG nommé *FrontEnd-NSG* en fonction du scénario ci-dessus, suivez les étapes ci-dessous :

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

2. Créer une règle de sécurité de l’accès à partir d’Internet sur le port 3389.

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix Internet -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 3389

3. Créer une règle de sécurité de l’accès à partir d’Internet vers le port 80.

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101
            -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix *
            -DestinationPortRange 80

4. Ajoutez ces règles créés ci-dessus afin qu’un nouveau NSG nommé **FrontEnd-NSG**.

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus
        -Name "NSG-FrontEnd" -SecurityRules $rule1,$rule2

5. Vérifiez les règles créées dans le NSG.

        $nsg

    Afficher uniquement les règles de sécurité de sortie :

        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

6. Associer le NSG créé ci-dessus au sous-réseau *frontal* .

                    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
                    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
                        -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

                Output showing only the *FrontEnd* subnet settings, notice the value for the **NetworkSecurityGroup** property:

                    Subnets           : [
                                          {
                                            "Name": "FrontEnd",
                                            "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                            "AddressPrefix": "192.168.1.0/24",
                                            "IpConfigurations": [
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                              },
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                              }
                                            ],
                                            "NetworkSecurityGroup": {
                                              "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                            },
                                            "RouteTable": null,
                                            "ProvisioningState": "Succeeded"
                                          }

    >[AZURE.WARNING] La sortie de la commande ci-dessus affiche le contenu de l’objet de configuration de réseau virtuel, qui n’existe que sur l’ordinateur où vous exécutez PowerShell. Vous devez exécuter la `Set-AzureRmVirtualNetwork` applet de commande pour enregistrer ces paramètres dans Azure.

7. Enregistrer les nouveaux paramètres de VNet pour Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Afficher uniquement la partie NSG de sortie :

        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Comment créer le NSG pour le sous-réseau back-end
Pour créer un NSG nommée *NSG-back-end* en fonction du scénario ci-dessus, suivez les étapes ci-dessous :

1. Créer une règle de sécurité de l’accès à partir du sous-réseau du front-end au port 1433 (port par défaut utilisé par SQL Server).

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 1433

2. Créer une règle de sécurité bloque l’accès à Internet.

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet"
            -Access Deny -Protocol * -Direction Outbound -Priority 200
            -SourceAddressPrefix * -SourcePortRange *
            -DestinationAddressPrefix Internet -DestinationPortRange *

3. Ajoutez ces règles créés ci-dessus afin qu’un nouveau NSG nommé **NSG-back-end**.

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-BackEnd"
            -SecurityRules $rule1,$rule2

4. Associer le NSG créé ci-dessus au sous-réseau *back-end* .

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

    Afficher uniquement les paramètres de sous-réseau *back-end* de la sortie, notez la valeur de la propriété **NetworkSecurityGroup** :

        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

5. Enregistrer les nouveaux paramètres de VNet pour Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet


## <a name="how-to-remove-an-nsg"></a>Comment faire pour supprimer un NSG

Pour supprimer un NSG existante, appelée *NSG-frontal* dans ce cas, suivez la procédure ci-dessous :

Exécuter **Suppression-AzureRmNetworkSecurityGroup** ci-dessous et veillez à inclure le NSG est dans le groupe de ressources.

            Remove-AzureRmNetworkSecurityGroup -Name "NSG-FrontEnd" -ResourceGroupName "TestRG"
