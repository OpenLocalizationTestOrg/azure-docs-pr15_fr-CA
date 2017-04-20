<properties 
   pageTitle="Comment définir un IP privée statique en mode ARM à l’aide de l’interface CLI | Microsoft Azure"
   description="Présentation des adresses IP statique (DIP) et comment les gérer en mode ARM à l’aide de l’interface CLI"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-azure-cli"></a>Comment définir une adresse IP privée dans Azure CLI

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes CLI d’Azure ci-dessous s’attendent à un environnement simple déjà créé. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test décrit dans [Création d’un vnet](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée lors de la création d’une machine virtuelle
Pour créer un ordinateur virtuel nommé *DNS01* dans le sous-réseau *frontal* d’un VNet nommé *TestVNet* avec un IP statique privée de *192.168.1.101*, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Sortie attendue :

        info:    New mode is arm

3. Exécutez l' **azure réseau public ip créer** pour créer une adresse IP publique pour l’ordinateur virtuel. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    Sortie attendue :

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g (ou des groupes de ressources)**. Nom de l’adresse IP publique sera créé dans le groupe de ressources.
    - **n-(ou--nom)**. Nom de l’adresse IP publique.
    - **-l (ou--emplacement)**. Région Azure où l’adresse IP publique sera créé. Pour notre scénario, il s’agit de *centralus*.

3. Exécutez la commande de **Création de la carte d’interface réseau réseau azure** pour créer une carte d’interface réseau avec un IP privée statique. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    Sortie attendue :

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-a (ou--privé-ip-address)**. Adresse IP privée statique pour la carte réseau.
    - **-m (ou--nom du vnet de sous-réseau)**. Nom de le VNet où la carte réseau sera créée.
    - **-k (ou--nom du sous-réseau)**. Nom du sous-réseau dans lequel la carte réseau sera créée.

4. Exécutez la commande **créer d’azure vm** pour créer la machine virtuelle à l’aide de l’adresse IP publique et carte réseau créé ci-dessus. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    Sortie attendue :

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y (ou - os-type)**. Type de système d’exploitation pour la machine virtuelle, *Windows* ou *Linux*.
    - **-f (ou le nom de la carte d’interface réseau :)**. Nom de la carte réseau de la machine virtuelle utilise.
    - **-i (ou--nom-public-ip)**. Nom de l’adresse IP publique, la machine virtuelle utilise.
    - **-F (ou--vnet-name)**. Nom de la VNet dans lequel l’ordinateur virtuel sera créé.
    - **-j (ou nom du sous-réseau : vnet)**. Nom du sous-réseau dans lequel l’ordinateur virtuel sera créé.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment faire pour extraire des informations statiques d’adresse IP privées pour un ordinateur virtuel

Pour afficher les informations d’adresse IP privées statiques pour la machine virtuelle créée avec le script ci-dessus, exécutez la commande suivante de la CLI d’Azure et observer les valeurs pour la *méthode d’allocation IP privée* et *adresse IP privée*:

    azure vm show -g TestRG -n DNS01

Sortie attendue :

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment faire pour supprimer une adresse IP privée statique à partir d’un ordinateur virtuel
Vous ne pouvez pas supprimer une adresse IP privée statique à partir d’une carte réseau dans l’infrastructure du langage commun Azure pour le Gestionnaire de ressources. Vous devez créer une nouvelle carte réseau qui utilise une adresse IP dynamique, supprimez la carte réseau précédente à partir de la machine virtuelle et puis ajouter la nouvelle carte réseau de la machine virtuelle. Pour modifier la carte réseau de l’ordinateur virtuel utilisé int eh commandes ci-dessus, suivez les étapes ci-dessous.
    
1. Exécutez la commande de **Création de la carte d’interface réseau réseau azure** pour créer une nouvelle carte réseau à l’aide d’allocation IP dynamique. Notez comment vous n’avez pas besoin de spécifier l’adresse IP à ce moment.

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    Sortie attendue :

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. Exécutez la commande **vm azure défini** pour modifier la carte d’interface réseau utilisée par la machine virtuelle.

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    Sortie attendue :

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. Si vous souhaitez, exécuter la commande **réseau azure nic supprimer** pour supprimer l’ancienne carte

        azure network nic delete -g TestRG -n TestNIC --quiet

    Sortie attendue :

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment faire pour ajouter une adresse IP privée pour un ordinateur virtuel existant
Pour ajouter une adresse IP privée pour la carte réseau utilisée par l’ordinateur virtuel créé à l’aide du script ci-dessus, exécutez la commande suivante :

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Sortie attendue :

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP publiques (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API de reste IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).
