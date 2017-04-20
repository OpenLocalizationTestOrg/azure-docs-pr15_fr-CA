<properties 
   pageTitle="Contrôler le routage et utiliser des boîtiers virtuels dans le Gestionnaire de ressources à l’aide de la CLI Azure | Microsoft Azure"
   description="Apprenez à contrôler le routage et utilisez les appliances virtuelles à l’aide de la CLI d’Azure"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Créer des itinéraires (UDR) défini par l’utilisateur dans l’interface CLI Azure

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer des UDRs dans le modèle de déploiement classique](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes CLI d’Azure ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, tout d’abord créer l’environnement de test par le déploiement de [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)et cliquez sur **déployer vers Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, suivez les instructions dans le portail.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Créer le UDR pour le sous-réseau de front-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de front-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`azure network route-table create`** commande pour créer une table de routage pour le sous-réseau de front-end.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Sortie :

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Paramètres :
    - **-g (ou des groupes de ressources)**. Nom du groupe de ressources où sera créé le UDR. Pour notre scénario, il s’agit de *TestRG*.
    - **-l (ou--emplacement)**. Région où sera créé le nouveau UDR Azure. Pour notre scénario, il s’agit de *westus*.
    - **n-(ou--nom)**. Nom de la nouvelle UDR. Pour notre scénario, *UDR-frontal*.

4. Exécuter le **`azure network route-table route create`** commande pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau back-end (192.168.2.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Sortie :

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Paramètres :
    - **-r (ou--nom de table de routage)**. Nom de la table de routage dans lequel l’itinéraire est ajouté. Pour notre scénario, *UDR-frontal*.
    - **-a (ou--préfixe d’adresse)**. Préfixe de l’adresse pour le sous-réseau où les paquets sont destinés à. Pour notre scénario, *192.168.2.0/24*.
    - **-y (ou--type du saut suivant)**. Type de trafic d’objet recevront. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *Aucun*.
    - **-p (ou--le saut suivant-adresse ip**). Adresse IP du tronçon suivant. Pour notre scénario, *192.168.0.4*.

5. Exécuter le **`azure network vnet subnet set`** commande pour associer la table de routage créée précédemment avec le sous-réseau **frontal** .

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Sortie :

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Paramètres :
    - **-e (ou--vnet-name)**. Nom de le VNet où se trouve le sous-réseau. Pour notre scénario, il s’agit de *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer le UDR pour le sous-réseau back-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de back-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécuter le **`azure network route-table create`** commande pour créer une table de routage pour le sous-réseau back-end.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Exécuter le **`azure network route-table route create`** commande pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau front-end (192.168.1.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Exécuter le **`azure network vnet subnet set`** commande pour associer la table de routage créée précédemment avec le sous-réseau **back-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Activer le transfert IP sur FW1
Pour activer le transfert de la carte réseau utilisée par **FW1**IP, suivez les étapes ci-dessous.

1. Exécuter le **`azure network nic show`** de commande et notez la valeur pour **le routage IP d’activer**. Elle doit être définie sur *false*.

        azure network nic show -g TestRG -n NICFW1

    Sortie :
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Exécuter le **`azure network nic set`** commande pour activer le routage IP.

        azure network nic set -g TestRG -n NICFW1 -f true

    Sortie :

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Paramètres :

    - **-f (ou--- ip-activer le transfert)**. *valeur True* ou *false*.
