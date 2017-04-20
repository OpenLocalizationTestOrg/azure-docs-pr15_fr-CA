<properties
   pageTitle="Comment créer des NSGs en mode classique, à l’aide de la CLI Azure | Microsoft Azure"
   description="Découvrez comment créer et déployer des NSGs en mode classique, à l’aide de la CLI d’Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Comment créer des NSGs (classic) dans l’infrastructure du langage commun Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [créer des NSGs dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes CLI d’Azure ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test en [créant un VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Comment créer le NSG pour le sous-réseau de front-end
Pour créer un NSG nommé nommé **NSG-FrontEnd** en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécuter le **`azure config mode`** commande pour basculer vers le mode classique, comme illustré ci-dessous.

        azure config mode asm

    Sortie attendue :

        info:    New mode is asm

3. Exécuter le **`azure network nsg create`** commande pour créer un NSG.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Sortie attendue :

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Paramètres :

    - **-l (ou--emplacement)**. Région où sera créé le nouveau NSG Azure. Pour notre scénario, il s’agit de *westus*.
    - **n-(ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG-frontal*.

4. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 3389 (RDP) à partir d’Internet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Sortie attendue :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Paramètres :

    - **-a (ou nom du nsg--)**. Nom de la NSG dans lequel la règle sera créée. Pour notre scénario, *NSG-frontal*.
    - **n-(ou--nom)**. Nom de la nouvelle règle. Pour notre scénario, *rdp-règle*.
    - **-c (ou action)**. Niveau d’accès de la règle (refuser et autoriser).
    - **-p (ou--protocole)**. Protocole (Tcp, Udp, ou *) de la règle.
    - **-r (ou type)**. Direction de la connexion (entrante ou sortante).
    - **-y (ou--priorité)**. Priorité de la règle.
    - **-f (ou--préfixe d’adresse source)**. Préfixe d’adresse source dans CIDR ou à l’aide de balises par défaut.
    - **-o (ou plage de ports source)**. Port source ou plage de ports.
    - **-e (ou--préfixe d’adresse destination)**. Préfixe d’adresse de destination dans CIDR ou à l’aide de balises par défaut.
    - **-u (ou plage de ports de destination)**. Port de destination, ou une plage de ports.

5. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 80 (HTTP) à partir d’Internet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Putput attendue :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Exécuter le **`azure network nsg subnet add`** commande à lier le NSG au sous-réseau front-end.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Sortie attendue :

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Comment créer le NSG pour le sous-réseau back-end
Pour créer un NSG nommée nommé *NSG-back-end* en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`azure network nsg create`** commande pour créer un NSG.

        azure network nsg create -l uswest -n NSG-BackEnd

    Sortie attendue :

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Paramètres :

    - **-l (ou--emplacement)**. Région où sera créé le nouveau NSG Azure. Pour notre scénario, il s’agit de *westus*.
    - **n-(ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG-frontal*.

4. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 1433 (SQL) à partir du sous-réseau du front-end.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Sortie attendue :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui refuse l’accès à Internet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Putput attendue :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Exécuter le **`azure network nsg subnet add`** commande à lier le NSG au sous-réseau back-end.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Sortie attendue :

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
