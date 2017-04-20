<properties 
   pageTitle="Résoudre les problèmes d’itinéraires - PowerShell | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes des itinéraires dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide de PowerShell d’Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-azure-powershell"></a>Résoudre les problèmes des gammes à l’aide de PowerShell d’Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Si vous rencontrez des problèmes de connectivité réseau vers ou à partir de votre Machine virtuelle (VM) Azure, itinéraires peuvent être ayant un impact sur les flux de trafic de votre machine virtuelle. Cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour les itinéraires afin de résoudre les autres.

Tables de routage sont associés à des sous-réseaux et sont efficaces sur toutes les interfaces réseau (NIC) de ce sous-réseau. Les types d’itinéraires suivants peuvent être appliquées à chaque interface réseau :

- **Des itinéraires de système :** Par défaut, chaque sous-réseau créé dans un réseau virtuel d’Azure (VNet) a des tables de routage de système qui autorisent le trafic local de VNet, le trafic local via des passerelles VPN et du trafic Internet. Itinéraires de système existent également pour les ressources VNets.
- **Itinéraires BGP :** Propagé aux interfaces réseau via ExpressRoute ou des connexions VPN de site à site. Plus d’informations sur le routage BGP en lisant les articles [BGP avec les passerelles VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) et [vue d’ensemble de ExpressRoute](../expressroute/expressroute-introduction.md) .
- **Les itinéraires définis par l’utilisateur (UDR) :** Si vous utilisez des équipements de réseau virtuel ou sont forcés de tunneling du trafic d’un réseau local via une connexion VPN de site à site, vous pouvez avoir des itinéraires définis par l’utilisateur (UDRs) associés à votre table de routage de sous-réseau. Si vous n’êtes pas familier avec UDRs, lisez l’article [les itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined-routes) .

Avec les itinéraires différents qui peuvent être appliqués à une interface réseau, il peut être difficile de déterminer les itinéraires d’agrégation sont efficaces. Pour vous aider à résoudre les problèmes de connectivité de réseau de machine virtuelle, vous pouvez afficher tous les itinéraires pour une interface réseau efficaces dans le modèle de déploiement du Gestionnaire de ressources Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>À l’aide d’itinéraires efficace pour résoudre les problèmes de flux de trafic de machine virtuelle

Cet article utilise le scénario suivant à titre d’exemple pour illustrer comment résoudre les problèmes des itinéraires pour une interface réseau efficaces :

Une machine virtuelle (*VM1*) connecté à la VNet (*VNet1*, préfixe : 10.9.0.0/16) ne parvient pas à se connecter à un VM(VM3) dans une VNet de ressources récemment (*VNet3*, préfixe 10.10.0.0/16). Il n’y a aucun UDRs ou BGP n’achemine appliqué à l’interface de réseau VM1-NIC1 connecté à la machine virtuelle, seuls les itinéraires de système sont appliquées.

Cet article explique comment faire pour déterminer la cause de l’échec de la connexion, à l’aide de la capacité de parcours efficace dans un modèle de déploiement de la gestion des ressources Azure.
Tandis que l’exemple utilise uniquement les itinéraires de système, les mêmes étapes peuvent être utilisés pour déterminer les échecs de connexion entrante et sortante sur n’importe quel type d’itinéraire.

>[AZURE.NOTE] Si votre machine virtuelle a plus d’une carte d’interface réseau attachée, vérifiez les itinéraires efficaces pour chacune des cartes réseau pour diagnostiquer des problèmes de connectivité réseau vers et à partir d’un ordinateur virtuel.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Afficher les gammes efficaces pour une machine virtuelle

Pour afficher les itinéraires d’agrégation qui sont appliqués à un ordinateur virtuel, procédez comme suit :

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les gammes efficaces pour une interface réseau

Pour afficher les itinéraires d’agrégation qui sont appliquées à une interface réseau, procédez comme suit :

1.  Démarrer une session PowerShell d’Azure et une connexion vers Azure. Si vous n’êtes pas familier avec PowerShell Azure, lisez l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2.  La commande suivante renvoie tous les itinéraires appliqués à une interface réseau nommée *VM1-NIC1* dans le groupe de ressources *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si vous ne connaissez pas le nom d’une interface réseau, tapez la commande suivante pour récupérer les noms de toutes les interfaces réseau dans un group.* de ressources

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    La sortie suivante est similaire à la sortie pour chaque itinéraire appliquée au sous-réseau d’à que la carte réseau est connectée :

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Notez ce qui suit dans la sortie :
    - **Nom**: nom de l’itinéraire efficace peut être vide, sauf spécification explicite, pour les itinéraires définis par l’utilisateur. 
    - **État**: indique l’état de la gamme efficace. Les valeurs possibles sont « Actifs » ou « Non valide »
    - **AddressPrefixes**: Spécifie le préfixe de l’adresse de l’itinéraire efficace dans la notation CIDR. 
    - **nextHopType**: indique le saut suivant pour l’itinéraire. Les valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Null*. Une valeur *Null* pour **nextHopType** dans un UDR peut indiquer un itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et le réseau virtuel appliance virtuelle n’est pas dans un état de mise en service ou l’exécution. Si **nextHopType** est *VPNGateway* et qu’il n’y a pas de passerelle mis en service ou l’exécution dans le VNet donné, l’itinéraire peut devenir non valide.
    - **NextHopIpAddress**: Spécifie l’adresse IP du tronçon suivant de l’itinéraire efficace.
    
    La commande suivante renvoie les itinéraires de manière plus afficher la table :

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    La sortie suivante est la partie de la sortie reçue pour le scénario décrit précédemment :

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Il n’y a aucun itinéraire répertorié sur le *WestUS-VNet3* VNet (préfixe 10.10.0.0/16)* * à partir de *WestUS-VNet1* (préfixe 10.9.0.0/16) dans la sortie de l’étape précédente. Comme indiqué dans l’image suivante, le lien d’homologation VNet avec la VNet *WestUS-VNet3* est dans un état *déconnecté* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Le lien bidirectionnel pour l’homologation est rompu, qui explique pourquoi VM1 pas pu se connecter à VM3 dans le VNet *WestUS-VNet3* . Le programme d’installation un lien d’homologation VNet bidirectionnel à nouveau pour *WestUS-VNet1* et de VNets de *WestUS-VNet3* . La sortie retournée une fois le lien d’homologation VNet correctement établi :

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Après avoir déterminé le problème, vous pouvez ajouter, supprimer, ou modifier des routes et acheminer des tables. Tapez la commande suivante pour afficher la liste des commandes utilisées pour cela :

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Considérations relatives à la

Quelques éléments à garder à l’esprit lors de la révision de la liste des itinéraires retournés :

- Le routage est basé sur la plus longue du préfixe correspondance (LPM) entre les UDRs, les itinéraires BGP et système. S’il existe plusieurs itinéraires avec la même correspondance de stratégies locales, puis un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :
    - Itinéraire de défini par l’utilisateur
    - Itinéraires BGP
    - Itinéraire du système (par défaut)

    Avec des itinéraires efficaces, vous ne pouvez voir parcours efficaces de correspondance de stratégies locales basée sur toutes les voies disponibles. En montrant comment les itinéraires sont en réalité évalués pour une carte de réseau donné, cela rend beaucoup plus facile résoudre les problèmes des itinéraires spécifiques qui peuvent être ayant un impact sur la connectivité vers/à partir de votre machine virtuelle.

- Si vous avez UDRs et que vous envoyez du trafic à un réseau virtuel solution matérielle-logicielle (à couloirs), avec *VirtualAppliance* comme **nextHopType**, assurez-vous que le transfert IP est activé sur l’à couloirs reçoit le trafic ou les paquets sont supprimés. 
- Si Forced tunneling est activée, tout le trafic Internet sortant est routé vers sur site. RDP/SSH à partir d’Internet à votre machine virtuelle peuvent ne pas fonctionner avec ce paramètre, en fonction de comment sur site gère ce trafic. 
  Forcé de tunneling peut être activé :
    - Si vous utilisez un réseau VPN de site à site, en définissant un itinéraire défini par l’utilisateur (UDR) avec nextHopType comme passerelle VPN
    - Si un itinéraire par défaut est annoncé sur BGP
- Pour VNet le trafic homologation fonctionne correctement, un itinéraire de système avec **nextHopType** *VNetPeering* doit exister pour la plage de préfixes de le VNet de ressources. Si un itinéraire de ce type n’existe pas, et le lien d’homologation VNet semble OK :
    - Attendez quelques secondes et réessayez s’il s’agit d’un lien d’homologation récemment établi. Il est parfois plus longue pour propager les itinéraires à toutes les interfaces réseau dans un sous-réseau.
    - Règles du groupe de sécurité réseau (NSG) peuvent être un impact sur les flux de trafic. Pour plus d’informations, consultez l’article de [Résoudre les groupes de sécurité réseau](virtual-network-nsg-troubleshoot-powershell.md) .
