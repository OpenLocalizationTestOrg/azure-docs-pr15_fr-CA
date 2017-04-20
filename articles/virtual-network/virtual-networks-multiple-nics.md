<properties
   pageTitle="Créer un ordinateur virtuel avec plusieurs cartes réseau"
   description="Découvrez comment créer et configurer des ordinateurs virtuels avec plusieurs cartes réseau"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Créer un ordinateur virtuel avec plusieurs cartes réseau

Vous pouvez créer des machines virtuelles (VM) dans Azure et attacher plusieurs interfaces réseau (cartes réseau) pour chacun de vos ordinateurs virtuels. Multi NIC est une exigence pour de nombreuses appliances virtuelles réseau, telles que la livraison des applications et des solutions d’optimisation WAN. Multi NIC fournit également plusieurs fonctionnalités de gestion du trafic réseau, y compris l’isolation du trafic entre une face avant fin de carte d’interface réseau et sauvegarder la fin, cartes réseau, ou la séparation de plan du trafic de données provenant du trafic de gestion plan.

![Carte d’interface réseau multiples de machine virtuelle](./media/virtual-networks-multiple-nics/IC757773.png)

La figure ci-dessus montre une machine virtuelle avec trois cartes réseau, que chacune connectée à un sous-réseau différent.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- Côté Internet VIP (déploiements classiques) est uniquement pris en charge sur la carte de réseau « par défaut ». Il y a un seul VIP pour l’adresse IP de la carte de réseau par défaut.
- À ce stade, adresses d’Instance au niveau IP publics (LPIP) (déploiements classiques) ne sont pas pris en charge pour plusieurs machines virtuelles de carte réseau.
- L’ordre des cartes réseau à partir de l’intérieur de la machine virtuelle sera aléatoire et peut également changer entre les mises à jour de l’infrastructure Azure. Toutefois, les adresses IP et les correspondants ethernet MAC en adresses seront restent les mêmes. Par exemple, supposons que **Eth1** a l’adresse IP 10.1.0.100 et adresse MAC 00-0D-3A-B0-39-0D ; après une mise à jour de l’infrastructure Azure et un redémarrage, il peut être modifié à **Eth2**, mais l’adresse IP et MAC appariement reste le même. Lorsqu’un redémarrage est lancée par le client, l’ordre de la carte d’interface réseau reste le même.
- L’adresse de chaque carte réseau sur chaque machine virtuelle doit se trouver dans un sous-réseau, plusieurs cartes réseau sur un ordinateur virtuel unique peut chacune être affectés les adresses qui sont dans le même sous-réseau.
- La taille de la mémoire virtuelle détermine le nombre de cartes réseau que vous pouvez créer pour un ordinateur virtuel. La taille de chaque machine virtuelle prend en charge [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) de référence et les articles de tailles de machine virtuelle [Linux](../virtual-machines/virtual-machines-linux-sizes.md) pour déterminer le nombre de cartes réseau. 

## <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSGs)
Dans un gestionnaire de ressources de déploiement, toute carte d’interface réseau sur un ordinateur virtuel peut être associé avec un réseau sécurité groupe (NSG), y compris les cartes réseau sur un ordinateur virtuel qui possède plusieurs cartes réseau activées. Si une adresse dans un sous-réseau dans lequel le sous-réseau est associé à un NSG est attribuée à une carte réseau, puis les règles dans NSG du sous-réseau s’appliquent également à cette carte réseau. En plus de l’association de sous-réseaux aux NSGs, vous pouvez également associer une carte réseau avec un NSG.

Si un sous-réseau est associé à un NSG et une carte réseau au sein de ce sous-réseau est individuellement associée à un NSG, les règles NSG associées sont appliquées dans l' **ordre du flux** en fonction du sens du trafic est transmis depuis ou vers la carte réseau :

- **Le trafic entrant** , dont la destination est la carte réseau en question se déroule tout d’abord le sous-réseau, déclencher de règles NSG du sous-réseau, avant le passage à la carte réseau, puis déclencher de règles de la carte d’interface réseau NSG.
- **Le trafic sortant** dont la source est la carte réseau en question sort tout d’abord à partir de la carte d’interface réseau, déclencher de règles de NSG la carte d’interface réseau, avant d’en passant par l’intermédiaire du sous-réseau, puis déclencher de règles NSG du sous-réseau.

En savoir plus sur les [Groupes de sécurité réseau](virtual-networks-nsg.md) et comment ils sont appliqués selon les associations pour les sous-réseaux, les ordinateurs virtuels et les cartes réseau...

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Comment faire pour configurer un NIC VM de multiples dans un déploiement classique

Les instructions ci-dessous vous aide à créer un plusieurs VM NIC contenant 3 cartes d’interface réseau : carte réseau par défaut et deux cartes d’interface réseau supplémentaires. Les étapes de configuration va créer un ordinateur virtuel qui sera configuré selon le fragment de fichier de configuration service ci-dessous :

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Vous devez les conditions préalables suivantes avant d’essayer d’exécuter les commandes PowerShell dans l’exemple.

- Un abonnement Azure.
- Un réseau virtuel configuré. Pour plus d’informations sur VNets, reportez-vous à la section [Vue d’ensemble de réseau virtuel](virtual-networks-overview.md) .
- La dernière version de PowerShell d’Azure téléchargé et installé. Voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Pour créer un ordinateur virtuel avec plusieurs cartes réseau, suivez les étapes ci-dessous :

1. Sélectionnez une image de machine virtuelle à partir de la galerie d’images Azure VM. Notez que les images changent souvent et sont disponibles par région. L’image spécifiée dans l’exemple ci-dessous peut modifier ou peut n’est pas dans votre région, veillez à spécifier l’image dont vous avez besoin.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Créer une configuration de machine virtuelle.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Créer la connexion de l’administrateur par défaut.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Ajouter des cartes réseau à la configuration de la machine virtuelle.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Spécifiez l’adresse IP et le sous-réseau de la carte de réseau par défaut.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Créer la machine virtuelle dans votre réseau virtuel.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] Le VNet que vous spécifiez ici doit déjà exister (comme indiqué dans les conditions préalables). L’exemple ci-dessous spécifie un réseau virtuel nommé **MultiNIC-VNet**.

## <a name="limitations"></a>Limitations

Les limitations suivantes sont applique lors de l’utilisation de la fonctionnalité de carte d’interface réseau multiples :

- Machines virtuelles de carte d’interface réseau multiples doivent être créés dans des réseaux virtuels Azure (VNets). Machines virtuelles de non-VNet ne peut pas être configuré avec plusieurs cartes d’interface réseau.
- Tous les ordinateurs virtuels dans un jeu de disponibilité doivent utiliser plusieurs cartes réseau ou une carte unique Il ne peut pas être un mélange d’ordinateurs virtuels de carte d’interface réseau multiples et machines virtuelles de carte d’interface réseau unique dans un ensemble de disponibilité. Mêmes règles s’appliquent pour les machines virtuelles dans un service cloud.
- Un ordinateur virtuel avec une carte réseau unique ne peuvent pas être configuré avec plusieurs cartes d’interface réseau (et vice versa) après son déploiement, sans supprimer et le recréer.


## <a name="secondary-nics-access-to-other-subnets"></a>Accès de cartes d’interface réseau secondaire vers d’autres sous-réseaux

Par défaut les NIC secondaire ne sera pas configuré avec une passerelle par défaut, en raison de laquelle le flux de trafic sur les cartes réseau secondaires sera limité à l’intérieur du même sous-réseau. Si les utilisateurs souhaitent activer les cartes d’interface réseau secondaires parler à l’extérieur de leur propre sous-réseau, ils devez ajouter une entrée dans la table de routage pour configurer la passerelle comme décrit ci-dessous.

>[AZURE.NOTE] Machines virtuelles avant juillet 2015 peut avoir une passerelle par défaut configurée pour toutes les cartes réseau. La passerelle par défaut pour les cartes réseau secondaire ne sera pas supprimée tant que ces machines virtuelles sont redémarrés. Dans les systèmes d’exploitation qui utilisent le modèle de routage d’hôte faible, tel que Linux, connectivité Internet peut interrompre si le trafic entrant et sortant utilise différentes cartes d’interface réseau.

### <a name="configure-windows-vms"></a>Configurer des ordinateurs virtuels de Windows

Supposons que vous ayez une VM Windows avec deux cartes réseau comme suit :

- Adresse IP de NIC principale : 192.168.1.4
- Adresse IP de NIC secondaire : 192.168.2.5

La table de routage IPv4 pour cette machine virtuelle ressemblerait à ceci :

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Notez que l’itinéraire par défaut (0.0.0.0) est uniquement disponible pour la carte principale Vous ne pourrez pas accéder aux ressources à l’extérieur du sous-réseau de la carte réseau secondaire, comme indiqué ci-dessous :

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Pour ajouter un itinéraire par défaut sur la carte réseau secondaire, procédez comme suit :

1. À partir d’une invite de commandes, exécutez la commande ci-dessous pour identifier le numéro d’index de la carte réseau secondaire :

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Notez la deuxième entrée dans la table, avec un index de 27 (dans cet exemple).
3. À partir de l’invite de commandes, exécutez la commande **route add** , comme illustré ci-dessous. Dans cet exemple, vous spécifiez 192.168.2.1 comme passerelle par défaut pour la carte réseau secondaire :

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Pour tester la connectivité, revenir à l’invite de commande, essayez de la commande ping sur un sous-réseau différent de celui de la carte réseau secondaire en tant qu’int indiqué eh exemple ci-dessous :

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Vous pouvez également vérifier votre table de routage pour vérifier l’itinéraire nouvellement ajouté, comme illustré ci-dessous :

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurer des ordinateurs virtuels de Linux

Pour les machines virtuelles de Linux, le comportement par défaut utilise l’hôte faible, routage, nous recommandons que les cartes réseau secondaires sont limités aux flux de trafic que dans le même sous-réseau. Toutefois si certains scénarios exigent la connectivité à l’extérieur du sous-réseau, les utilisateurs doivent activer basée de routage pour garantir que le trafic entrant et sortant utilise la même carte réseau.

## <a name="next-steps"></a>Étapes suivantes

- Déployer des [Machines virtuelles de MultiNIC dans un scénario d’application de niveau 2 dans un déploiement du Gestionnaire de ressources](virtual-network-deploy-multinic-arm-template.md).
- Déployer des [Machines virtuelles de MultiNIC dans un scénario d’application de niveau 2 dans un déploiement classique](virtual-network-deploy-multinic-classic-ps.md).
