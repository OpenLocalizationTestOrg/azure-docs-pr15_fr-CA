<properties 
   pageTitle="Contrôler le routage et utilisez les appliances virtuelles à l’aide de la CLI d’Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à contrôler le routage dans VNets à l’aide de la CLI d’Azure dans le modèle de déploiement classique"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Contrôle le routage et l’utilisation des boîtiers virtuels (classique) à l’aide de la CLI d’Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [contrôle le routage et l’utilisation des appliances virtuelles dans le modèle de déploiement du Gestionnaire de ressources](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes CLI d’Azure ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, créez l’environnement [créer un VNet (classique) à l’aide de la CLI d’Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Créer le UDR pour le sous-réseau de front-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de front-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécuter le **`azure config mode`** pour basculer vers le mode classique.

        azure config mode asm

    Sortie :

        info:    New mode is asm

3. Exécuter le **`azure network route-table create`** commande pour créer une table de routage pour le sous-réseau de front-end.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Sortie :

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Paramètres :
    - **-l (ou--emplacement)**. Région où sera créé le nouveau NSG Azure. Pour notre scénario, il s’agit de *westus*.
    - **n-(ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG-frontal*.

4. Exécuter le **`azure network route-table route set`** commande pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau back-end (192.168.2.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Sortie :

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Paramètres :
    - **-r (ou--nom de table de routage)**. Nom de la table de routage dans lequel l’itinéraire est ajouté. Pour notre scénario, *UDR-frontal*.
    - **-a (ou--préfixe d’adresse)**. Préfixe de l’adresse pour le sous-réseau où les paquets sont destinés à. Pour notre scénario, *192.168.2.0/24*.
    - **-t (ou--type du saut suivant)**. Type de trafic d’objet recevront. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *Aucun*.
    - **-p (ou--le saut suivant-adresse ip**). Adresse IP du tronçon suivant. Pour notre scénario, *192.168.0.4*.

5. Exécuter le **`azure network vnet subnet route-table add`** commande pour associer la table de routage créée précédemment avec le sous-réseau **frontal** .

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Sortie :

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Paramètres :
    - **-t (ou--vnet-name)**. Nom de le VNet où se trouve le sous-réseau. Pour notre scénario, il s’agit de *TestVNet*.
    - **- n (ou--nom du sous-réseau**. Nom du sous-réseau que sera ajoutée à la table de routage. Pour notre scénario, *site Web frontal*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer le UDR pour le sous-réseau back-end
Pour créer la table de routage et de la gamme requise pour le sous-réseau de back-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`azure network route-table create`** commande pour créer une table de routage pour le sous-réseau back-end.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Exécuter le **`azure network route-table route set`** commande pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau front-end (192.168.1.0/24) pour la machine virtuelle (192.168.0.4) **FW1** .

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Exécuter le **`azure network vnet subnet route-table add`** commande pour associer la table de routage créée précédemment avec le sous-réseau **back-end** .

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

